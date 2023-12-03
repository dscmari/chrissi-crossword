<template>
    <div id="canvas-container">
        <form ref="form" action="#">
                <!--  -->
        </form>
        <canvas ref="canvas" @click="handleCanvasClick"></canvas>
 
    </div>
</template>


<script>
import questions from "/src/assets/questions.json";
export default {
    data(){
        return {
            questions : questions
        };
    },
    mounted() {
        this.initCanvas();
    },
    methods: {
        initCanvas() {
            const canvas = this.$refs.canvas;
            const context = canvas.getContext('2d');
            
            // Style the canvas
            canvas.width = 800;
            canvas.height = 800;
            context.fillStyle = 'white';
            context.fillRect(0, 0, canvas.width, canvas.height);
            const lineWidth = 0.3;
            context.lineWidth = lineWidth;

            // Get the canvas's position relative to its container
            var canvasLeft = canvas.offsetLeft;
            var canvasTop = canvas.offsetTop;

            const numColumns = 16;
            const numRows = 15;

            // Calculate cell size based on canvas dimensions and grid size
            const cellWidth = canvas.width / numColumns;
            const cellHeight = canvas.height / numRows;

            // Draw the grid
            for (let i = 0; i < numColumns; i++) {
                for (let j = 0; j < numRows; j++) {
                    // Draw the cell boundaries
                    context.strokeRect(i * cellWidth, j * cellHeight, cellWidth, cellHeight);
                }
            }

            const arrowRight = "right";
            const arrowDown = "down"

            //Create array with crossword questions
            const questionsArray = []
            for(let i = 0; i<15; i++){
                const question = this.getQuestion(i);
                questionsArray.push(question)
            }

            this.createQuestionCell(1,0, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[0], arrowDown)
            this.createQuestionCell(15,5, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[1], arrowDown)
            this.createQuestionCell(10,11, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[2], arrowRight)
            this.createQuestionCell(5,2, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[3], arrowRight)
            this.createQuestionCell(4,2, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[4], arrowDown)
            this.createQuestionCell(6,4, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[5], arrowDown)
            this.createQuestionCell(7,9, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[6], arrowRight)
            this.createQuestionCell(12,1, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[7], arrowDown)
            this.createQuestionCell(2,7, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[8], arrowRight)
            this.createQuestionCell(8,1, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[9], arrowDown)
            this.createQuestionCell(0,3, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[10], arrowRight)
            this.createQuestionCell(5,11, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[11], arrowRight)
            this.createQuestionCell(11,2, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[12], arrowRight)
            this.createQuestionCell(10,13, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[13], arrowRight)
            this.createQuestionCell(4,13, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[14], arrowRight)
        },
 
        getCellCoordinates(column, row, cellWidth, cellHeight) {
            return {
                x: column * cellWidth,
                y: row * cellHeight,
            };
        },

        createQuestionCell(x,y, cellWidth, cellHeight, canvasLeft,canvasTop, lineWidth, question, arrowDirection) {
            const cellCoordinates = this.getCellCoordinates(x, y, cellWidth, cellHeight);
            const cellDiv = document.createElement('div');

            cellDiv.style.position = 'absolute';
            cellDiv.style.left = `${canvasLeft + cellCoordinates.x + (lineWidth) * 10}px`;
            cellDiv.style.top = `${canvasTop + cellCoordinates.y + (lineWidth) * 10}px`;
            cellDiv.style.width = `${cellWidth}px`;
            cellDiv.style.height = `${cellHeight}px`;
            cellDiv.style.display = 'flex';
            cellDiv.style.alignItems = 'center';


            const questionElement = this.createQuestion(question)
            cellDiv.appendChild(questionElement);
            
            const arrow = this.createArrow(arrowDirection)
            cellDiv.appendChild(arrow)

            const container = document.getElementById('canvas-container');
            container.appendChild(cellDiv);
        },

        createQuestion(question){
            const p = document.createElement('p')
            p.textContent = question

            return p;
        },

        getQuestion(questionNumber){
            const questionObjct = Object.values(this.questions)[questionNumber];
            const question = Object.keys(questionObjct)[0];
            return question;
        },

        createArrow(arrowDirection){
            const arrow = document.createElement('img');
            arrow.classList.add('arrow');
            arrow.style.height = '12px';
            arrow.style.width = '12px';
         
            if(arrowDirection === 'right'){
                arrow.src = '/img/arrow-right.png';
                arrow.style.transform = 'translateX(-5px)';
            } else {
                arrow.src = '/img/arrow-down.png';
                arrow.style.transform = 'translateX(-35px) translateY(30px)';
            }
            return arrow;
        },

        handleCanvasClick(event) {
            const form = this.$refs.form;
            console.log(form)
            
            const inputX = event.offsetX;
            const inputY = event.offsetY;
            console.log(inputX)
            console.log(inputY)
            const inputCell = document.createElement('input')
            inputCell.style.position = 'absolute'
            inputCell.style.height = '50px';
            inputCell.style.width = '100px';
            inputCell.style.backgroundColor = 'red';
            inputCell.style.left = inputX + 'px';
            inputCell.style.top = inputY + 'px';
            inputCell.value = 'Your Text Here'; // Or any content you want to display

            inputCell.style.zIndex = "20"
          
            console.log(inputCell)
            form.appendChild(inputCell); //here the input cell is not visible
            // document.body.appendChild(inputCell) //this works, its visible
            console.log(`Clicked at coordinates relative to canvas: (${inputX}, ${inputY})`);

        },
    },
};
</script>

<style>

*{
    padding: 0;
    margin: 0;
}

canvas {
    display: block;
    position: relative;
    border: solid 3px green;
}
#canvas-container{
    position: relative;
    background-color: blue;
    padding: 1rem;
}

p{
    min-width: 100%;
    font-size: 12px;
    padding: 0;
    margin: 0px;
    line-height: 1;
    word-wrap: break-word;
    z-index: 2;
}
.arrow{
    z-index: 1;
}

form{
    position: relative;
    height: 10px;
    width: 20px;
    background-color: yellow;
}
</style>