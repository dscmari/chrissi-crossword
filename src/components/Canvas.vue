<template>
    <canvas ref="canvas" @click="handleCanvasClick"></canvas>
</template>


<script>
export default {
    mounted() {
        this.initCanvas();
    },
    methods: {
        initCanvas() {
            const canvas = this.$refs.canvas;
            // Set up your canvas properties and context
            const ctx = canvas.getContext('2d');
            

            // Style the canvas
            canvas.width = 800;
            canvas.height = 600;
            ctx.fillStyle = 'white';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.lineWidth = 0.3;

            const numColumns = 14;
            const numRows = 12;

            // Calculate cell size based on canvas dimensions and grid size
            const cellWidth = canvas.width / numColumns;
            const cellHeight = canvas.height / numRows;

            // Draw the grid
            for (let i = 0; i < numColumns; i++) {
                for (let j = 0; j < numRows; j++) {
                    // Draw the cell boundaries
                    ctx.strokeRect(i * cellWidth, j * cellHeight, cellWidth, cellHeight);
                }
            }
        },
        handleCanvasClick(event) {
            // Handle the click event, create input fields, etc.
        },
    },
};
</script>

<style>
canvas {
    border: solid 3px green;
}
</style>