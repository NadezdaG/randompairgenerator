<script setup>
import { ref, reactive } from "vue";


let list1 = reactive([1, 2, 3, 4])
let list2 = reactive([1, 2, 3, 4])
let shuffledList = ref([])
let newItem1 = ref('');
let newItem2 = ref('');
let error = ref(false);
let shuffled = ref(false);
let overlay = ref(false);
let count = ref(10);
let errorText = "Lists must be of equal length."

const remove = (index, list) => {
    list.splice(index, 1);
    clearInputs();
}

const addNew = (list, item) => {
    list.push(item);
    clearInputs();
}

const clearInputs = () => {
    newItem1.value = '';
    newItem2.value = '';
    shuffledList.value = [];
    error.value = false;
    shuffled.value = false;
}

const randomiser = () => {
    console.log(list1.length, list2.length);
    shuffled.value = true;
    overlay.value = true;
    let i = 0;
    const interval = setInterval(function () {
        i++;
        count.value = 10 - i;
        if (list1.length != list2.length) {
            error.value = true;
            shuffledList.value = [];
            return;
        }
        let arr1 = shuffleArray(list1);
        let arr2 = shuffleArray(list2);
        shuffledList.value = arr1.map((item, index) => [item, arr2[index]]);
        if (i >= 10) {
            clearInterval(interval);
            overlay.value = false;
        }
    }, 1000);


}

const shuffleArray = (array) => {
    const newArray = [...array]
    const length = newArray.length
    for (let start = 0; start < length; start++) {
        const randomPosition = Math.floor((newArray.length - start) * Math.random())
        const randomItem = newArray.splice(randomPosition, 1)
        newArray.push(...randomItem)
    }
    return newArray
}
</script>

<template>
    <header>
        <img alt="random pair generator" class="rpg-logo" src="./assets/dalle-e-randomiser-image.png" />
        <h1>random pair generator</h1>
    </header>

    <main>
        <div class="rpg-list rpg-list--1">
            <h2>List 1</h2>
            <ul>
                <li v-for="(item, index) in list1">{{ item }} <span @click="remove(index, list1)">(remove)</span></li>
                <li>
                    <form @submit.prevent="addNew(list1, newItem1)">
                        <input placeholder="add new..." v-model="newItem1">
                        <button type="submit">Add</button>
                    </form>
                </li>
            </ul>
        </div>

        <div class="rpg-list rpg-list--2">
            <h2>List 2</h2>
            <ul>
                <li v-for="(item, index) in list2">{{ item }} <span @click="remove(index, list2)">(remove)</span></li>
                <li>
                    <form @submit.prevent="addNew(list2, newItem2)">
                        <input placeholder="add new..." v-model="newItem2">
                        <button type="submit">Add</button>
                    </form>
                </li>
            </ul>
        </div>

        <div class="rgp-results">

            <button @click="randomiser" v-if="!shuffled">RANDOMISE</button>
            <ul v-if="shuffled">
                <li v-for="(pair) in shuffledList">{{ pair[0] }} {{ pair[1] }}</li>
            </ul>

            <div v-if="error">{{ errorText }}</div>
            <div class="rgp-results__overlay" v-if="overlay">
                <span>{{ count }}</span>
            </div>
        </div>
    </main>
</template>

<style lang="scss">
@use "assets/scss/reset";
@import url('https://fonts.googleapis.com/css2?family=Martian+Mono:wght@200;400&display=swap');

:root {
    --color-red: red;
    --color-black: black;
    --color-white: white;
}

html {
    height: 100%;
}

body {
    min-height: 100%;
    background-color: white;
    padding: 3em;
    font-size: 16px;
    line-height: 1;
    font-weight: 200;
    font-family: "Martian Mono";
    box-sizing: border-box;
    display: flex;
}

header {
    display: flex;
    text-align: center;
    padding-bottom: 2em;
}

main {
    display: grid;
    grid-template-rows: 1fr 1fr;
    grid-template-columns: 0.5fr 1.5fr;
    grid-template-areas: "list1 results" "list2 results";
}

input,
button {
    padding: 8px 10px;
    border: none;
    border-bottom: 1px solid var(--color-black);
}

button {
    background: var(--color-black);
    color: var(--color-white);
    text-transform: uppercase;
    cursor: pointer;
    letter-spacing: 2px;
}

.rpg-list {
    padding: 0.5em 0;

    h2 {
        margin-bottom: 0.5em;
    }

    ul {
        display: flex;
        flex-direction: column;
        gap: 10px;

        li {
            span {
                font-size: 0.7em;
                color: var(--color-red);
                cursor: pointer;
            }

        }
    }

    &--1 {
        grid-area: list1;
    }

    &--2 {
        grid-area: list2;
    }
}

.rgp-results {
    grid-area: results;
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;

    &__overlay {
        display: flex;
        position: absolute;
        background: rgba(255, 255, 255, 0.8);
        top: 0;
        left: 0;
        z-index: 30;
        width: 100%;
        height: 100%;
        font-size: 3em;
        justify-content: center;
        align-items: center;
    }

    button {
        background-color: var(--color-red);
        font-size: 1.2em;
        border: none;

    }

    ul {
        font-size: 1.5em;
        display: flex;
        flex-direction: column;
        gap: 0.5em;

        li {
            padding: 0.5em 0;
            border-bottom: 1px dashed var(--color-black)
        }
    }
}

#app {
    display: grid;
    grid-template-columns: 1fr;
    grid-template-rows: auto 1fr;
    min-height: 100%;
    position: relative;
    width: 100%;
}

.rpg-logo {
    height: 50px;
    width: auto;
    display: inline;
}

h1 {
    font-size: 50px;
    text-transform: uppercase;
    font-weight: 400;
}

h2 {
    font-size: 25px;
    font-weight: 400;
}
</style>
