<template>
    <div class="flex-container">
        <p>Lösungswort: </p>
        <ul>
            <li v-for="char in solutionCells" :key="char">{{ char }}</li>
        </ul>
    </div>
</template>

<script>

export default {
    props: {
        solutionCells: Array,
        testArray: Array
    }
};


</script>

<style scoped>
.flex-container {
    display: flex;
    align-items: center;
    margin-top: 1rem;
    padding: 0.5rem;
    background-color: white;
    opacity: 0.9;
    width: fit-content;
    border-radius: 8px;
}

p {
    font-family: 'Comic Sans MS', cursive, sans-serif;
    font-size: 24px;
    font-weight: bold;
    margin: 0;
    padding: 0;
}

ul {
    display: flex;
    list-style: none;
    margin: 0;
}

li {
    width: 50px;
    height: 50px;
    border: solid 3px #0e2431;
    margin-left: 3px;
    margin-right: 3px;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: white;
    font-family: 'Comic Sans MS', cursive, sans-serif;
    font-size: 40px;
}
</style>