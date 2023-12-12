<template>
    <div id="canvas-container">
        <form ref="form" action="#">
            <!-- dynamically created input -->
        </form>
        <canvas ref="canvas" @click="handleCanvasClick"></canvas>
        <Solution :solutionCells="solutionCells" />
    </div>
</template>


<script>
import questions from "/src/assets/questions.json";
import crossword2D from "/src/assets/crossword2D";
import Solution from "./Solution.vue";

export default {
    data() {
        return {
            crossword2D: crossword2D,
            solutionCells: []
        };
    },
    components: {
        Solution
    },
    mounted() {
        this.initCanvas();
    },
    methods: {
        initCanvas() {
            this.getLocalStorage()
            this.updateSolution()

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
        getFontSize(cellContent) {
            return cellContent.length > 1 ? '12px serif' : '24px serif';
        },

        drawGrid(canvas, context, cols, rows, cellWidth, cellHeight) {
            context.clearRect(0, 0, canvas.width, canvas.height);
            // Draw the grid
            const lineWidth = 0.3;
            context.lineWidth = lineWidth;
            context.textAlign = 'center';
            context.textBaseline = 'middle';
            for (let i = 0; i < cols; i++) {
                for (let j = 0; j < rows; j++) {
                    // Draw the cell boundaries
                    context.strokeRect(i * cellWidth, j * cellHeight, cellWidth, cellHeight);
                    // Draw the word with custom line breaks within the cell
                    const cellContent = crossword2D["crossword2D-questions"][j][i];
                    // Set font size based on the length of cell content
                    context.font = this.getFontSize(cellContent);
                    // Check if the cell content is '-'
                    if (cellContent === '-') {
                        // Draw an empty string with a red background for the cell
                        context.fillStyle = '#F40009';
                        context.fillRect(i * cellWidth, j * cellHeight, cellWidth, cellHeight);
                        context.fillStyle = 'black';  // Reset the fill color
                    } else {
                        const maxLineWidth = cellWidth - 10;
                        const words = cellContent.split(' ');
                        let line = '';
                        let lines = [];
                        let currentLineWidth = 0;
                        for (let word of words) {
                            const wordWidth = context.measureText(word + ' ').width;

                            // Check if the word itself is too long for the cell
                            if (wordWidth > maxLineWidth) {
                                // Break the word into smaller segments
                                let segment = '';
                                for (let char of word) {
                                    const charWidth = context.measureText(char).width;
                                    if (currentLineWidth + charWidth <= maxLineWidth) {
                                        // Character fits within the line
                                        segment += char;
                                        currentLineWidth += charWidth;
                                    } else {
                                        // Start a new line with the current segment
                                        // Add a hyphen before the break
                                        if (segment !== '') {
                                            lines.push(segment + '-');
                                        }
                                        segment = char;
                                        currentLineWidth = charWidth;
                                    }
                                }
                                // Add the last segment
                                if (segment !== '') {
                                    lines.push(segment);
                                }
                            } else {
                                // Check if adding the word would exceed the maxLineWidth
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
                        }
                        // Add the last line
                        lines.push(line.trim());
                        // Draw each line within the same cell
                        let y = j * cellHeight; // Adjust the vertical positioning
                        lines.forEach(line => {
                            context.fillText(line, i * cellWidth + cellWidth / 2, y + cellHeight / 2);
                            y += 10; // Adjust the line spacing
                        });
                    }

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
            const crosswordState = localStorage.getItem('crosswordState');
            if (crosswordState) {
                crossword2D["crossword2D-questions"] = JSON.parse(crosswordState);
            }
        },

        updateSolution() {
            this.solutionCell1 = crossword2D["crossword2D-questions"][3][3]; //G
            this.solutionCell2 = crossword2D["crossword2D-questions"][7][4]; //L
            this.solutionCell3 = crossword2D["crossword2D-questions"][5][1]; //U
            this.solutionCell4 = crossword2D["crossword2D-questions"][11][15]; //E
            this.solutionCell5 = crossword2D["crossword2D-questions"][2][1]; //C
            this.solutionCell6 = crossword2D["crossword2D-questions"][2][12]; //K

            let solutionCells = [];
            for (let i = 1; i < 7; i++) {
                solutionCells.push(this["solutionCell" + i]);
            }
            this.solutionCells = solutionCells;
        },

        checkIfEmptyCell() {
            const isEmpty = x => x === "";
            return crossword2D["crossword2D-questions"].some(row => row.some(isEmpty));
        },

        are2DArraysEqual() {
        console.log("check if equal called")
            const arrayUser = crossword2D["crossword2D-questions"];
            const arrayCorrect = crossword2D["crossword2D"]
            for (let i = 0; i < arrayUser.length; i++) {
                for (let j = 0; j < arrayUser.length; j++) {
                    if (arrayUser[i][j] !== arrayCorrect[i][j]) {
                        console.log("i:"+i+" j:" + j+ ' ' + arrayUser[i][j])
                        console.log("i:"+i+" j:" + j + ' ' + arrayCorrect[i][j])
                        return false;
                    }
                }
            }
            console.log("true is returned")
            return true;
        },

        checkIfFinished() {
            const hasEmptyCell = this.checkIfEmptyCell();
            if (!hasEmptyCell) {
                if (this.are2DArraysEqual()) {
                    alert("you won hurrai")
                }
            }
        },

        handleCanvasClick(event) {
            const form = this.$refs.form;
            const canvas = this.$refs.canvas;
            const context = canvas.getContext('2d');

            const cols = 16;
            const rows = 15;
            const cellWidth = canvas.width / cols;
            const cellHeight = canvas.height / rows;
            let clickedColumn = Math.floor(event.offsetX / cellWidth);
            let clickedRow = Math.floor(event.offsetY / cellHeight);

            //do not create input field if its red cell or question
            if (crossword2D["crossword2D-questions"][clickedRow][clickedColumn] === '-' || crossword2D["crossword2D-questions"][clickedRow][clickedColumn].length > 1 ) {
                return;
            }
            const input = document.createElement('input')
            input.type = 'text';
            input.maxLength = 1;

            //size input element
            input.style.height = cellHeight + 'px';
            input.style.width = cellWidth + 'px';
            input.style.maxHeight = cellHeight + 'px';
            input.style.maxWidth = cellWidth + 'px';

            //position input elements
            //+3px because of the outer border
            input.style.position = 'absolute'
            input.style.left = `${clickedColumn * cellWidth + 3}px`;
            input.style.top = `${clickedRow * cellHeight + 3}px`;

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
            let currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];

            //TODO change element to append to? Is form correct?
            form.appendChild(input);
           
            input.focus();

            input.addEventListener('input', event => {
                // Get the input value and transform to uppercase
                let inputValue = event.target.value.toUpperCase();

                // Ensure that the input only contains A-Z
                inputValue = inputValue.replace(/[^A-Z]/g, '');

                userInput = inputValue.charAt(0);

                if (userInput) {
                    crossword2D["crossword2D-questions"][clickedRow][clickedColumn] = userInput;

                    //pass solution cells to solution component
                    this.updateSolution()
                    this.updateLocalStorage();
                    this.drawGrid(canvas, context, cols, rows, cellWidth, cellHeight)
                    this.checkIfFinished()

                }
            });

            input.addEventListener('keydown', event => {

                event.target.value = '';

                if (event.key === 'Delete' || event.key === 'Backspace') {
                    // do not delete questions
                    if (crossword2D["crossword2D-questions"][clickedRow][clickedColumn].length > 1) {
                        return
                    }
                    crossword2D["crossword2D-questions"][clickedRow][clickedColumn] = '';
                    input.placeholder = '';
                    this.updateSolution();
                } else if (event.key === 'ArrowLeft') {
                    if(crossword2D["crossword2D-questions"][clickedRow][clickedColumn - 1].length > 1 || crossword2D["crossword2D-questions"][clickedRow][clickedColumn -1] === '-'){
                        while(crossword2D["crossword2D-questions"][clickedRow][clickedColumn - 1].length > 1 || crossword2D["crossword2D-questions"][clickedRow][clickedColumn -1] === '-'){
                            clickedColumn--;
                            if(clickedColumn === 0){
                                clickedColumn = 16
                            }
                        }
                    }
                    clickedColumn--;
                    currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                    input.placeholder = currentCell;               
                } else if (event.key === 'ArrowRight') {
                    if(clickedColumn === 15 || crossword2D["crossword2D-questions"][clickedRow][clickedColumn + 1].length > 1 || crossword2D["crossword2D-questions"][clickedRow][clickedColumn +1] === '-'){
                        while(clickedColumn === 15 || crossword2D["crossword2D-questions"][clickedRow][clickedColumn + 1].length > 1 || crossword2D["crossword2D-questions"][clickedRow][clickedColumn +1] === '-'){
                            if(clickedColumn === 15){
                                clickedColumn = 0
                            } else clickedColumn++;
                        }
                    }
                    clickedColumn++;
                    currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                    input.placeholder = currentCell; 
                } else if (event.key === 'ArrowUp') {
                    if(clickedRow === 0 || crossword2D["crossword2D-questions"][clickedRow - 1][clickedColumn].length > 1 || crossword2D["crossword2D-questions"][clickedRow - 1][clickedColumn] === '-'){
                        while(clickedRow === 0 || crossword2D["crossword2D-questions"][clickedRow - 1][clickedColumn].length > 1 || crossword2D["crossword2D-questions"][clickedRow - 1][clickedColumn] === '-'){
                            console.log("in while")
                            if(clickedRow === 0){
                                clickedRow = 15
                            } else clickedRow--;
                        }
                    }
                    clickedRow--;
                    currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                    input.placeholder = currentCell; 
                } else if (event.key === 'ArrowDown') {
                    if(clickedRow === 14 || crossword2D["crossword2D-questions"][clickedRow + 1][clickedColumn].length > 1 || crossword2D["crossword2D-questions"][clickedRow + 1][clickedColumn] === '-'){
                        while(clickedRow === 14 || crossword2D["crossword2D-questions"][clickedRow + 1][clickedColumn].length > 1 || crossword2D["crossword2D-questions"][clickedRow + 1][clickedColumn] === '-'){
                            console.log("in while")
                            if(clickedRow === 14){
                                clickedRow = -1
                            } else clickedRow++;
                        }
                    }
                    clickedRow++;
                    currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                    input.placeholder = currentCell; 
                }

                // Set the updated position
                input.style.left = `${clickedColumn * cellWidth + 3}px`;
                input.style.top = `${clickedRow * cellHeight + 3}px`;

                this.updateLocalStorage();
                this.drawGrid(canvas, context, cols, rows, cellWidth, cellHeight);
            });

            input.addEventListener('focus', () => {
                // Get the content of the current cell
                const cellContent = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                console.log("focus event fires");

                // Check if the content of the cell is bigger than 1
                if (cellContent.length > 1) {
                    // Store the value of the current cell when focused
                    currentCell = cellContent;
                    input.placeholder = currentCell;
                    console.log(cellContent);
                }
            });

            // Remove the input element on blur
            input.addEventListener('blur', function () {
                form.removeChild(input);
            });


        },
    },
};
</script>

<style scoped>
* {
    padding: 0;
    margin: 0;
}

canvas {
    display: block;
    position: relative;
    border: solid 3px black;
}

#canvas-container {
    position: relative;
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
    z-index: 30;
}
</style>