<template>
    <div id="canvas-container">
        <form ref="form" action="#">
            <!-- dynamically created input -->
        </form>
        <canvas ref="canvas" @click="handleCanvasClick"></canvas>
    </div>
    <Solution :solutionCells="solutionCells" />
</template>


<script>
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

            const cellWidth = canvas.width / cols;
            const cellHeight = canvas.height / rows;

            this.drawGrid(canvas, context, cols, rows, cellWidth, cellHeight)
        },

        getFontSize(cellContent) {
            return cellContent.length > 1 ? '12px serif' : '32px Comic Sans MS';
        },

        drawGrid(canvas, context, cols, rows, cellWidth, cellHeight) {
            context.clearRect(0, 0, canvas.width, canvas.height);
            // Draw the grid
            const lineWidth = 0.3;
            context.lineWidth = lineWidth;
            context.textAlign = 'left';
            context.textBaseline = 'middle';
            for (let i = 0; i < cols; i++) {
                for (let j = 0; j < rows; j++) {
                    // Draw the cell boundaries
                    context.strokeRect(i * cellWidth, j * cellHeight, cellWidth, cellHeight);
                    // Draw the word with custom line breaks within the cell
                    let cellContent = crossword2D["crossword2D-questions"][j][i];
                    // Set font size based on the length of cell content
                    context.font = this.getFontSize(cellContent);
                    
                    //no content cell
                    if (cellContent === '-') {
                        context.fillStyle = '#0e2431';
                        context.fillRect(i * cellWidth, j * cellHeight, cellWidth, cellHeight);
                        context.fillStyle = 'black';
                    } else {
                        //draw Arrows
                        const lastChar = cellContent[cellContent.length - 1];
                        const arrowSize = 7;
                        context.fillStyle = 'black';
                        if(lastChar === 'R' && cellContent.length > 1){
                            context.beginPath();
                            context.moveTo((i + 1) * cellWidth, (j + 0.5) * cellHeight - arrowSize / 2);
                            context.lineTo((i + 1) * cellWidth, (j + 0.5) * cellHeight + arrowSize / 2);
                            context.lineTo((i + 1) * cellWidth + arrowSize, (j + 0.5) * cellHeight);
                            context.closePath();
                            context.fill();
                            cellContent = cellContent.substring(0, cellContent.length - 1)
                        } else if(lastChar === 'B' && cellContent.length > 1){
                            context.beginPath();
                            context.moveTo((i + 0.5) * cellWidth - arrowSize / 2, (j + 1) * cellHeight);
                            context.lineTo((i + 0.5) * cellWidth + arrowSize / 2, (j + 1) * cellHeight);
                            context.lineTo((i + 0.5) * cellWidth, (j + 1) * cellHeight + arrowSize);
                            context.closePath();
                            context.fill();
                            cellContent = cellContent.substring(0, cellContent.length - 1)
                        }
                        
                        //adjust position cell content
                        let lines = []
                        let y = j * cellHeight; //adjust line starts
                        let y2 = 2; //adjust vertical position
                        let x = 8 //adjust horizontal position
                        //break words if too long (marked in crossword2D.json file)
                        if(cellContent.includes('-')){
                            let wordParts = cellContent.split('-')
                            for(let wordPart of wordParts ){
                                lines.push(wordPart)
                            }
                            if(lines.length === 2){
                                    y2 = 2.5
                                }
                            else if (lines.length === 3){
                                    y2 = 3
                                }
                            let isCentered = lines.every(wordPart => wordPart.length < 7)
                            if(isCentered){
                                x = 4;
                            }
                            lines.forEach(line => {
                                if(line !== lines[lines.length -1]){
                                    if(Array.from(line).every(char => char !== '/')){
                                        line = line + '-'
                                    }      
                                }
                                line = line.replace('/','')                          
                                context.fillText(line, i * cellWidth + cellWidth / x, y + cellHeight / y2);
                                y += 14;
                            })
                        } else{
                            if(cellContent.length === 1){
                                x = 2.6;
                            }
                            else if(cellContent.length < 7){
                                x = 6;
                            }
                            context.fillText(cellContent, i * cellWidth + cellWidth / x, y + cellHeight / 2);
                        }
                    }

                }
            }
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
            const arrayUser = crossword2D["crossword2D-questions"];
            const arrayCorrect = crossword2D["crossword2D"]
            for (let i = 0; i < arrayUser.length; i++) {
                for (let j = 0; j < arrayUser.length; j++) {
                    if (arrayUser[i][j] !== arrayCorrect[i][j]) {
                        console.log("arrays are not equal")
                        console.log(arrayUser[i][j])
                        console.log(i +" "+j)
                        return false;
                    }
                }
            }
            console.log("arrays are equal")
            return true;
        },

        checkIfFinished() {
            const hasEmptyCell = this.checkIfEmptyCell();
            if (!hasEmptyCell) {
                console.log("no cell empty")
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
            input.style.height = cellHeight - 1.5 + 'px';
            input.style.width = cellWidth -1.5 + 'px';
            input.style.maxHeight = cellHeight + 'px';
            input.style.maxWidth = cellWidth + 'px';

            //position input elements
            input.style.position = 'absolute'
            input.style.left = `${clickedColumn * cellWidth}px`;
            input.style.top = `${clickedRow * cellHeight}px`;

            //style input element
            input.style.padding = '0px';
            input.style.border = 'none';
            input.style.outline = 'none'
            input.style.textAlign = 'center';
            input.style.backgroundColor = '#D3D3D3';
            input.style.transition = 'background-color 0.3s ease'
            input.style.fontSize = '28px';
            input.style.fontFamily = 'Comic Sans MS';

            let userInput = '';
            let currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];

            //TODO change element to append to? Is form correct?
            form.appendChild(input);
        
            input.focus();
            
            input.addEventListener('input', event => {

                //reset current cell to prevent input text and cell content overlap
                crossword2D["crossword2D-questions"][clickedRow][clickedColumn] = "";
                this.drawGrid(canvas, context, cols, rows, cellWidth, cellHeight)
           
                let inputValue = event.target.value.toUpperCase();
             
                // Ensure that the input only contains A-Z
                inputValue = inputValue.replace(/[^A-Z]/g, '');
                userInput = inputValue.charAt(0);

                if (userInput) {
                    crossword2D["crossword2D-questions"][clickedRow][clickedColumn] = userInput;

                    //pass solution cells to solution component
                    this.updateSolution()
                    this.updateLocalStorage();
                    
                    //this.drawGrid(canvas, context, cols, rows, cellWidth, cellHeight)
                    this.checkIfFinished()
                }
                
            });

            document.addEventListener('click', (event) => {
                this.drawGrid(canvas, context, cols, rows, cellWidth, cellHeight);
            });



            input.addEventListener('keydown', event => {

                event.target.value = '';

                if (event.key === 'Delete' || event.key === 'Backspace') {
                    // do not delete questions
                    if (crossword2D["crossword2D-questions"][clickedRow][clickedColumn].length > 1) {
                        return
                    }
                    crossword2D["crossword2D-questions"][clickedRow][clickedColumn] = '';
                    // input.placeholder = '';
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
                    // input.placeholder = currentCell;
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
                    // input.placeholder = currentCell; 
                } else if (event.key === 'ArrowUp') {
                    if(clickedRow === 0 || crossword2D["crossword2D-questions"][clickedRow - 1][clickedColumn].length > 1 || crossword2D["crossword2D-questions"][clickedRow - 1][clickedColumn] === '-'){
                        while(clickedRow === 0 || crossword2D["crossword2D-questions"][clickedRow - 1][clickedColumn].length > 1 || crossword2D["crossword2D-questions"][clickedRow - 1][clickedColumn] === '-'){
                            if(clickedRow === 0){
                                clickedRow = 15
                            } else clickedRow--;
                        }
                    }
                    clickedRow--;
                    currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                    // input.placeholder = currentCell; 
                } else if (event.key === 'ArrowDown') {
                    if(clickedRow === 14 || crossword2D["crossword2D-questions"][clickedRow + 1][clickedColumn].length > 1 || crossword2D["crossword2D-questions"][clickedRow + 1][clickedColumn] === '-'){
                        while(clickedRow === 14 || crossword2D["crossword2D-questions"][clickedRow + 1][clickedColumn].length > 1 || crossword2D["crossword2D-questions"][clickedRow + 1][clickedColumn] === '-'){
                            if(clickedRow === 14){
                                clickedRow = -1
                            } else clickedRow++;
                        }
                    }
                    clickedRow++;
                    currentCell = crossword2D["crossword2D-questions"][clickedRow][clickedColumn];
                    // input.placeholder = currentCell;
                }
                // Set the updated position
                input.style.left = `${clickedColumn * cellWidth}px`;
                input.style.top = `${clickedRow * cellHeight}px`;

                this.updateLocalStorage();
                this.drawGrid(canvas, context, cols, rows, cellWidth, cellHeight);
            });

            // Remove the input element on blur
            input.addEventListener('blur', function () {
                form.removeChild(input);
            });
            canvas.style.zIndex = "40";
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
    z-index: 40;

}

#canvas-container {
    position: relative;
    background-color: white;
    border: solid 1px #0e2431;
    opacity: 0.9;
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

form {
    position: relative;
    z-index: 30;
}
</style>