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
    solutionCells : Array,
    testArray : Array
  }
};


</script>

<style scoped>

.flex-container{
    display: flex;
    padding: 12px;
    align-items: center;
}

p{
    font-size: 24px;
}

ul{
    display: flex;
    list-style: none;
}

li{
    width: 50px;
    height: 50px;
    border: solid 3px #0e2431;
    margin: 1.5px;
    display: flex;
    align-items: center;
    justify-content: center;
}
</style>