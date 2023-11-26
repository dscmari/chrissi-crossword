<template>
    <div id="canvas-container">
        <canvas ref="canvas" @click="handleCanvasClick"></canvas>
    </div>
</template>


<script>
export default {
    mounted() {
        this.initCanvas();
    },
    methods: {
        initCanvas() {
            const canvas = this.$refs.canvas;
            const context = canvas.getContext('2d');
            
            // Style the canvas
            canvas.width = 800;
            canvas.height = 600;
            context.fillStyle = 'white';
            context.fillRect(0, 0, canvas.width, canvas.height);
            const lineWidth = 0.3;
            context.lineWidth = lineWidth;

            // Get the canvas's position relative to its container
            var canvasLeft = canvas.offsetLeft;
            var canvasTop = canvas.offsetTop;

            const numColumns = 14;
            const numRows = 12;

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
            this.createQuestionCell(0,0, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, "Frage 1")
            this.createQuestionCell(2,3, cellWidth, cellHeight, canvasLeft, canvasTop, lineWidth, "Frage 2")
            // this.createCellDiv(10,10, cellWidth, cellHeight, canvasRect, lineWidth)
        },
 
        getCellCoordinates(column, row, cellWidth, cellHeight) {
            return {
                x: column * cellWidth,
                y: row * cellHeight,
            };
        },

        createQuestionCell(x,y, cellWidth, cellHeight, canvasLeft,canvasTop, lineWidth, question) {
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
            cellDiv.style.backgroundColor = 'red';

        
            const questionElement = this.createQuestion(question)
            
            cellDiv.appendChild(questionElement);

            const container = document.getElementById('canvas-container');
            container.appendChild(cellDiv);
        },

           
        createQuestion(question){
            const p = document.createElement('p')
            p.textContent = question;

            return p;
        },

        handleCanvasClick(event) {
            // Handle the click event, create input fields, etc.
        },
    },
};
</script>

<style>
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
    padding: 2px;
    margin: 0;
}
</style>