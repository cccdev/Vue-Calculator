<script setup>
import { ref } from 'vue'

const equation = ref('0')
const isDemicalAdded = ref(false)
const isOperatorAdded = ref(false)
const isStarted = ref(false)


const isOperator = (character) => {
  return ['+', '-', '×', '÷'].indexOf(character) > -1;
}

const append = (character) => {
  // 若为0且并非加减乘除
  if (equation.value === '0' && !isOperator(character)) {
    if (character === '.') {
      equation.value += '' + character;
      isDemicalAdded.value = true;
    } else {
      equation.value = '' + character;
    }
    isStarted.value = true;
    return;
  }

  // 若输入的并非加减乘除
  if (!isOperator(character)) {


    if (character === '.' && isDemicalAdded.value === true) {
      return;
    }
    if (character === '.') {
      isDemicalAdded.value = true;
      isOperatorAdded.value = true;
    } else {
      isOperatorAdded.value = false;
    }
    equation.value += '' + character;
  }

  // 若输入的是运算符
  if (isOperator(character)) {
    if (isOperatorAdded.value === true) {
      return;
    }
    isDemicalAdded.value = false;
    isOperatorAdded.value = true;
    equation.value += '' + character;
  }

}

//点击 = 时
const calculate = () => {
  let result = equation.value.replace(new RegExp('×', 'g'), '*')
    .replace(new RegExp('÷', 'g'), '/');
  equation.value = parseFloat(eval(result).toFixed(9)).toString();
  isOperatorAdded.value = false;
  isDemicalAdded.value = false;

}

// 点击 ± 时
const calculateToggle = () => {
  if (isOperatorAdded.value || !isStarted.value) { return; }

  equation.value = equation.value + '* -1';
  this.calculate();
}

// 点击 % 时
const calculatePercentage = () => {
  if (isOperatorAdded.value || !isStarted.value) { return; }
  equation.value = equation.value + '* 0.01';
  this.calculate();

}

// 点击 AC 时
const clear = () => {
  equation.value = '0';
  isDemicalAdded.value = false;
  isOperatorAdded.value = false;
  isStarted.value = false;
}

// 回退
const back = () => {
  let len = equation.value.length;
  if (len === 1 || (equation.value[0] === '-' && len === 2)) {
    equation.value = '0';
  } else {
    equation.value = equation.value.substr(0, equation.value.length - 1);
  }
}
</script>

<template>
  <div class="calculator">
    <div class="result" style="grid-area: result">{{ equation }}</div>
    <button style="grid-area: ac" @click="clear">AC</button>
    <button style="grid-area: plus-minus" @click="calculateToggle">±</button>
    <button style="grid-area: percent" @click="calculatePercentage">%</button>
    <button style="grid-area: add" @click="append('+')">+</button>
    <button style="grid-area: subtract" @click="append('-')">-</button>
    <button style="grid-area: multiply" @click="append('×')">×</button>
    <button style="grid-area: divide" @click="append('÷')">÷</button>
    <button style="grid-area: equal" @click="calculate">=</button>

    <button style="grid-area: number-1" @click="append(1)">1</button>
    <button style="grid-area: number-2" @click="append(2)">2</button>
    <button style="grid-area: number-3" @click="append(3)">3</button>
    <button style="grid-area: number-4" @click="append(4)">4</button>
    <button style="grid-area: number-5" @click="append(5)">5</button>
    <button style="grid-area: number-6" @click="append(6)">6</button>
    <button style="grid-area: number-7" @click="append(7)">7</button>
    <button style="grid-area: number-8" @click="append(8)">8</button>
    <button style="grid-area: number-9" @click="append(9)">9</button>
    <button style="grid-area: number-0" @click="append(0)">0</button>

    <button style="grid-area: dot" @click="append('.')">.</button>
    <button style="grid-area: back" @click="back">⬅</button>
  </div>
</template>

<style>
/* 页面背景为浅黑，flex布局且元素水平垂直居中 */
body {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #eee;
}

/* 计算器整体拟态化 */
.calculator {
  --button-width: 80px;
  --button-height: 80px;

  display: grid;
  grid-template-areas:
    "result result result result"
    "back back back back"
    "ac plus-minus percent divide"
    "number-7 number-8 number-9 multiply"
    "number-4 number-5 number-6 subtract"
    "number-1 number-2 number-3 add"
    "number-0 number-0 dot equal";
  grid-template-columns: repeat(4, var(--button-width));
  grid-template-rows: repeat(6, var(--button-height));

  box-shadow: 
/* 分别是上方，左方阴影，模糊半径，扩散半径 颜色及透明度*/ -8px -8px
      16px -10px rgba(255, 255, 255, 1),
    /* 分别是下方，右方阴影，模糊半径，扩散半径 颜色及透明度*/ 8px 8px 16px -10px
      rgb(0, 0, 0, 0.15);

  padding: 24px;
  border-radius: 20px;
}

/* 按钮拟态化 */
.calculator button {
  margin: 8px;
  padding: 0;
  border: 0;
  display: block;
  outline: none;
  border-radius: calc(var(--button-width) / 2);
  font-size: 24px;
  font-family: Helvetica;
  font-weight: normal;
  color: #999;
  background: linear-gradient(
    135deg,
    rgb(230, 230, 230, 1) 0%,
    rgb(246, 246, 246, 1) 100%
  );
  box-shadow: -4px -4px 10px rgba(255, 255, 255, 1),
    4px 4px 10px -8px rgba(0, 0, 0, 0.3);
}

/* 设定按钮按下去的样式 */
.calculator button:active {
  /* inset 关键字是将阴影转移到内部 */
  box-shadow: -4px -4px 10px rgba(255, 255, 255, 1) inset,
    4px 4px 10px -8px rgba(0, 0, 0, 0.3) inset;
}

.result {
  overflow: hidden;
  text-align: right;
  line-height: var(--button-width);
  font-size: 48px;
  font-family: Helvetica;
  padding: 0 20px;
  color: #666;
}
</style>
