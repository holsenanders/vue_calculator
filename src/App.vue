<script setup>
import { ref } from 'vue';

const currentDisplayString = ref("0");
const lastAnswer = ref("0");
const logData = ref([]);

function init() {
  currentDisplayString.value = "0";
}

function handleButtonClick(event) {
  event.preventDefault();
  const button = event.target;
  const action = button.dataset.action;
  const buttonContent = button.textContent;
  const displayString = currentDisplayString.value;

  if (!action) {
    if (displayString === "0") {
      currentDisplayString.value = buttonContent;
    } else {
      currentDisplayString.value = displayString + buttonContent;
    }
  }

  if (action === "decimal") {
    if (!displayString.includes(".")) {
      currentDisplayString.value = displayString + ".";
    }
  }

  if (["add", "subtract", "multiply", "divide"].includes(action)) {
    currentDisplayString.value = displayString + " " + buttonContent + " ";
  }

  if (action === "equals") {
    try {
      const sanitizedInput = currentDisplayString.value.replace(/,/g, ".");
      const answer = Function(`'use strict'; return (${sanitizedInput})`)();
      currentDisplayString.value = answer.toString().replace(/\./g, ",");
      lastAnswer.value = answer;
      currentDisplayString.value = answer.toString();
      logData.value.push({ expression: displayString, answer });
    } catch (e) {
      currentDisplayString.value = "Error";
    }
  }

  if (action === "clear") {
    currentDisplayString.value = "0";
  }

  if (action === "delete") {
    currentDisplayString.value = displayString.slice(0, -1) || "0";
  }

  if (action === "ans") {
    currentDisplayString.value = displayString + lastAnswer.value;
  }
}
</script>


<template>
  <main>
    <form class="calculator">
      <input type="text" class="calculator-display" aria-label="Calculator Display" v-model="currentDisplayString" >
      <div class="calculator-buttons">
        <!-- First Row -->
        <button class="button action" data-action="clear" @click="handleButtonClick">C</button>
        <button class="button action" data-action="ans" @click="handleButtonClick">ANS</button>
        <button class="button action" data-action="delete" @click="handleButtonClick">DEL</button>
        <button class="button operator" data-action="divide" @click="handleButtonClick">/</button>

        <!-- Second Row -->
        <button class="button number" @click="handleButtonClick">7</button>
        <button class="button number" @click="handleButtonClick">8</button>
        <button class="button number" @click="handleButtonClick">9</button>
        <button class="button operator" data-action="subtract" @click="handleButtonClick">-</button>

        <!-- Third Row -->
        <button class="button number" @click="handleButtonClick">4</button>
        <button class="button number" @click="handleButtonClick">5</button>
        <button class="button number" @click="handleButtonClick">6</button>
        <button class="button operator" data-action="multiply" @click="handleButtonClick">*</button>

        <!-- Fourth Row -->
        <button class="button number" @click="handleButtonClick">1</button>
        <button class="button number" @click="handleButtonClick">2</button>
        <button class="button number" @click="handleButtonClick">3</button>
        <button class="button operator" data-action="add" @click="handleButtonClick">+</button>

        <!-- Fifth Row -->
        <button class="button number" data-action="blank"></button>
        <button class="button number" @click="handleButtonClick">0</button>
        <button class="button number" data-action="decimal" @click="handleButtonClick">.</button>
        <button class="button operator" data-action="equals" @click="handleButtonClick">=</button>
      </div>
    </form>
    <section class="log">
      <h2 class="log-title">Log:</h2>
      <ul>
        <li v-for="(entry, index) in logData" :key="index">
          {{ entry.expression }} = {{ entry.answer }}
        </li>
      </ul>
    </section>

  </main>
</template>

<style scoped>

.calculator-buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(5, 1fr);
  gap: 10px;
  padding: 10px;
}

.calculator-display {
  width: 90%;
  padding: 5px;
  font-size: 24px;
  text-align: right;
  background-color: #292d31;
  border: #292d31;;
  color: #f5f5f5;
}

.button {
  border-radius: 50%;
  width: 50px;
  height: 50px;
  color: #ffffff;
  font-size: 15px;
}

.action{
  background-color: #707478;
  border: #707478;
}

.operator{
  background-color: #ff9500;
  border: #ff9500;
}

.number{
  background-color: #52565a;
  border: #52565a;
}

.log {
  margin-top: 20px;
  padding: 10px;
  height: 200px;
  background-color: #f5f5f5;
  color: #222222;
}

.log-title {
  color: #222222;
}

</style>
