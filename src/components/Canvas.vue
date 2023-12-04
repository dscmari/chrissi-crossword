<template>
    <div id="canvas-container">
        <form ref="form" action="#">
            <!-- dynamically created input -->
        </form>
        <canvas ref="canvas" @click="handleCanvasClick"></canvas>
    </div>
</template>


<script>
import questions from "/src/assets/questions.json";
import crossword2D from "/src/assets/crossword2D";

export default {
    data() {
        return {
            crossword2D: crossword2D
        };
    },
    mounted() {
        this.initCanvas();
    },
    methods: {
        initCanvas() {
            this.getLocalStorage()


            const canvas = this.$refs.canvas;
            const context = canvas.getContext('2d');

            canvas.width = 1000;
            canvas.height = 1000;


            const cols = 16;
            const rows = 15;

            // Calculate cell size based on canvas dimensions and grid size
            const cellWidth = canvas.width / cols;
            const cellHeight = canvas.height / rows;


            this.drawGrid(canvas, context, cols, rows, cellWidth, cellHeight)

            // const arrowRight = "right";
            // const arrowDown = "down";

        },

        drawGrid(canvas, context, cols, rows, cellWidth, cellHeight) {
            console.log("drawGrid called")
            context.clearRect(0, 0, canvas.width, canvas.height);

            // Draw the grid

            const lineWidth = 0.3;
            context.lineWidth = lineWidth;
            context.font = "12px serif";
            context.textAlign = 'center';
            context.textBaseline = 'middle';

            for (let i = 0; i < cols; i++) {
                for (let j = 0; j < rows; j++) {

                    // Draw the cell boundaries
                    context.strokeRect(i * cellWidth, j * cellHeight, cellWidth, cellHeight);


                    // Draw the word with custom line breaks within the cell


                    const cellContent = crossword2D["crossword2D-questions"][j][i];

                    const maxLineWidth = cellWidth;
                    const words = cellContent.split(' ');

                    let line = '';
                    let lines = [];
                    let currentLineWidth = 0;

                    for (let word of words) {
                        const wordWidth = context.measureText(word + ' ').width;

                        if (currentLineWidth + wordWidth <= maxLineWidth) {
                            // Word fits within the line
                            line += word + ' ';
                            currentLineWidth += wordWidth;
                        } else {
                            // Start a new line with the current word
                            lines.push(line.trim());
                            line = word + ' ';
                            currentLineWidth = wordWidth;
                        }
                    }

                    // Add the last line
                    lines.push(line.trim());

                    // Draw each line within the same cell
                    let y = j * cellHeight - lines.length / 100; // Adjust the vertical positioning
                    lines.forEach(line => {
                        context.fillText(line, i * cellWidth + cellWidth / 2, y + cellHeight / 2);
                        y += 10; // Adjust the line spacing
                    });
                }
            }
        },

        createArrow(arrowDirection) {
            const arrow = document.createElement('img');
            arrow.classList.add('arrow');
            arrow.style.height = '12px';
            arrow.style.width = '12px';

            if (arrowDirection === 'right') {
                arrow.src = '/img/arrow-right.png';
                arrow.style.transform = 'translateX(-5px)';
            } else {
                arrow.src = '/img/arrow-down.png';
                arrow.style.transform = 'translateX(-35px) translateY(30px)';
            }
            return arrow;
        },

        updateLocalStorage() {
            localStorage.setItem('crosswordState', JSON.stringify(crossword2D["crossword2D-questions"]));
        },

        getLocalStorage() {
            const storedState = localStorage.getItem('crosswordState');
            if (storedState) {
                crossword2D["crossword2D-questions"] = JSON.parse(storedState);
            }
        },

        handleCanvasClick(event) {
            const form = this.$refs.form;
            const canvas = this.$refs.canvas;
            const context = canvas.getContext('2d');


            const inputX = event.offsetX;
            const inputY = event.offsetY;
            const input = document.createElement('input')
            input.type = 'text';
            input.maxLength = 1;


            //size input element
            const cols = 16;
            const rows = 15;
            const cellWidth = canvas.width / cols;
            const cellHeight = canvas.height / rows;
            input.style.height = cellHeight + 'px';
            input.style.width = cellWidth + 'px';
            input.style.maxHeight = cellHeight + 'px';
            input.style.maxWidth = cellWidth + 'px';

            //position input elements
            input.style.position = 'absolute'
            let clickedColumn = Math.floor(event.offsetX / cellWidth);
            let clickedRow = Math.floor(event.offsetY / cellHeight);
            input.style.left = `${clickedColumn * cellWidth + 2}px`;
            input.style.top = `${clickedRow * cellHeight + 12}px`;

            //style input element
            // input.style.backgroundColor = 'red';
            input.style.padding = '0px';
            input.style.border = 'none';
            input.style.textAlign = 'center';
            input.style.backgroundColor = '#D3D3D3';
            input.style.transition = 'background-color 0.3s ease'
            input.style.fontSize = '28px';
            input.placeholder = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];


            let userInput = '';
            let currentCell = '';

            //TODO change element to append to? Is form correct?
            form.appendChild(input);
            input.focus();

            input.addEventListener('input', event => {
                // Get the input value and transform to uppercase
                let inputValue = event.target.value.toUpperCase();

                // Ensure that the input only contains A-Z
                inputValue = inputValue.replace(/[^A-Z]/g, '');

                // Set the transformed value back to the input
                event.target.value = inputValue;

                userInput = inputValue.charAt(0);

                if (userInput) {
                    crossword2D["crossword2D-questions"][clickedRow][clickedColumn] = userInput;

                    this.updateLocalStorage();
                    const canvas = this.$refs.canvas;
                    const context = canvas.getContext('2d');
                    const cols = 16;
                    const rows = 15;
                    canvas.width = 1000;
                    canvas.height = 1000;
                    const cellWidth = canvas.width / cols;
                    const cellHeight = canvas.height / rows;

                    this.drawGrid(canvas, context, cols, rows, cellWidth, cellHeight)
                }
            });


            input.addEventListener('keydown', event => {

                if (event.key === 'Delete' || event.key === 'Backspace') {
                    // Handle delete key, clear the value in the cell
                    if (crossword2D["crossword2D-questions"][clickedRow][clickedColumn].length > 1) {
                        return
                    }
                    crossword2D["crossword2D-questions"][clickedRow][clickedColumn] = '';
                    input.placeholder = '';
                } else if (event.key === 'ArrowLeft' && clickedColumn > 0) {
                    // Move left if not at the leftmost column
                    clickedColumn--;
                    currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                    input.placeholder = currentCell;
                } else if (event.key === 'ArrowRight' && clickedColumn < cols - 1) {
                    // Move right if not at the rightmost column
                    clickedColumn++;
                    currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                    input.placeholder = currentCell;
                } else if (event.key === 'ArrowUp' && clickedRow > 0) {
                    // Move up if not at the top row
                    clickedRow--;
                    currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                    input.placeholder = currentCell;
                } else if (event.key === 'ArrowDown' && clickedRow < rows - 1) {
                    // Move down if not at the bottom row
                    clickedRow++;
                    currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                    input.placeholder = currentCell;
                }

                // Set the updated position
                input.style.left = `${clickedColumn * cellWidth + 2}px`;
                input.style.top = `${clickedRow * cellHeight + 12}px`;

                this.updateLocalStorage();
                this.drawGrid(canvas, context, cols, rows, cellWidth, cellHeight);
            });

            input.addEventListener('focus', () => {
                // Store the value of the current cell when focused
                currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                input.placeholder = currentCell;
            });

            // Remove the input element on blur
            input.addEventListener('blur', function () {
                form.removeChild(input);
            });


        },
    },
};
</script>

<style>
* {
    padding: 0;
    margin: 0;
}

canvas {
    display: block;
    position: relative;
    border: solid 3px green;
}

#canvas-container {
    position: relative;
    border: solid 3px blue;
    padding: 1rem;
}

p {
    min-width: 100%;
    font-size: 12px;
    padding: 0;
    margin: 0px;
    line-height: 1;
    word-wrap: break-word;
    z-index: 2;
}

.arrow {
    z-index: 1;
}

form {
    position: relative;
    height: 10px;
    width: 20px;
    background-color: yellow;
    z-index: 30;
}
</style>