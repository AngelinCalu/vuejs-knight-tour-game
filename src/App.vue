<script setup>
import ChessBoard from "./components/ChessBoard.vue";
</script>

<template>
    <div>
        <h1 class="m-4 font-bold text-2xl">Knight's Tour</h1>
        <p class="m-4 py-2 text-sm">The knight starts from any square and moves according to the rules of a knight's move in chess (two squares in one direction and one square in a perpendicular direction). <br>
            The objective is to visit all the squares on the board exactly once, without revisiting any square.</p>
        <ChessBoard />
    </div>
</template>
