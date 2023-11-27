<template>
    <div id="canvas-container">
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
            canvas.width = 1000;
            canvas.height = 1000;
            context.fillStyle = 'white';
            context.fillRect(0, 0, canvas.width, canvas.height);
            const lineWidth = 0.3;
            context.lineWidth = lineWidth;

            // Get the canvas's position relative to its container
            var canvasLeft = canvas.offsetLeft;
            var canvasTop = canvas.offsetTop;

            const numColumns = 18;
            const numRows = 18;

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

            
            //TODO loop
            const questionsArray = []
            for(let i = 0; i<15; i++){
                const question = this.getQuestion(i);
                questionsArray.push(question)
            }

            this.createQuestionCell(4,2, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[0], arrowRight)
            this.createQuestionCell(2,13, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[1], arrowRight)
            this.createQuestionCell(10,8, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[2], arrowDown)
            this.createQuestionCell(5,6, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[3], arrowDown)
            this.createQuestionCell(15,5, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[4], arrowDown)
            this.createQuestionCell(4,5, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[5], arrowRight)
            this.createQuestionCell(7,7, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[6], arrowDown)
            this.createQuestionCell(0,11, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[7], arrowRight)
            this.createQuestionCell(12,1, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[8], arrowDown)
            this.createQuestionCell(8,0, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, questionsArray[9], arrowDown)
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

            // Berücksichtigen Sie den Scroll-Offset
            const scrollX = window.scrollX;
            const scrollY = window.scrollY;

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
            // Handle the click event, create input fields, etc.
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
</style>