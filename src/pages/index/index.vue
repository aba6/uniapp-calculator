<template>
  <view class="calculator">
    <view class="display">
      <view class="history">{{ history }}</view>
      <view class="current">{{ current || '0' }}</view>
    </view>
    <view class="keypad">
      <view class="row">
        <button class="btn functional" @click="clear">AC</button>
        <button class="btn functional" @click="toggleSign">+/-</button>
        <button class="btn functional" @click="percent">%</button>
        <button class="btn operator" @click="appendOperator('/')">÷</button>
      </view>
      <view class="row">
        <button class="btn" @click="appendNumber('7')">7</button>
        <button class="btn" @click="appendNumber('8')">8</button>
        <button class="btn" @click="appendNumber('9')">9</button>
        <button class="btn operator" @click="appendOperator('*')">×</button>
      </view>
      <view class="row">
        <button class="btn" @click="appendNumber('4')">4</button>
        <button class="btn" @click="appendNumber('5')">5</button>
        <button class="btn" @click="appendNumber('6')">6</button>
        <button class="btn operator" @click="appendOperator('-')">-</button>
      </view>
      <view class="row">
        <button class="btn" @click="appendNumber('1')">1</button>
        <button class="btn" @click="appendNumber('2')">2</button>
        <button class="btn" @click="appendNumber('3')">3</button>
        <button class="btn operator" @click="appendOperator('+')">+</button>
      </view>
      <view class="row">
        <button class="btn zero" @click="appendNumber('0')">0</button>
        <button class="btn" @click="appendDot">.</button>
        <button class="btn operator" @click="calculate">=</button>
      </view>
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const current = ref('')
const history = ref('')
const operator = ref<string | null>(null)
const previousValue = ref('')
const resetDisplay = ref(false)

const clear = () => {
  current.value = ''
  history.value = ''
  operator.value = null
  previousValue.value = ''
}

const toggleSign = () => {
  if (current.value) {
    current.value = (parseFloat(current.value) * -1).toString()
  }
}

const percent = () => {
  if (current.value) {
    current.value = (parseFloat(current.value) / 100).toString()
  }
}

const appendNumber = (num: string) => {
  if (resetDisplay.value) {
    current.value = num
    resetDisplay.value = false
  } else {
    current.value = current.value === '0' ? num : current.value + num
  }
}

const appendDot = () => {
  if (!current.value.includes('.')) {
    current.value = current.value === '' ? '0.' : current.value + '.'
  }
}

const appendOperator = (op: string) => {
  if (current.value === '' && op !== '-') return
  
  if (previousValue.value && operator.value && !resetDisplay.value) {
    calculate()
  }
  
  operator.value = op
  previousValue.value = current.value
  history.value = `${previousValue.value} ${op}`
  resetDisplay.value = true
}

const calculate = () => {
  if (!operator.value || !previousValue.value || !current.value) return
  
  const prev = parseFloat(previousValue.value)
  const curr = parseFloat(current.value)
  let result = 0
  
  switch (operator.value) {
    case '+': result = prev + curr; break
    case '-': result = prev - curr; break
    case '*': result = prev * curr; break
    case '/': result = curr !== 0 ? prev / curr : 0; break
  }
  
  history.value = `${previousValue.value} ${operator.value} ${current.value} =`
  current.value = result.toString()
  operator.value = null
  previousValue.value = ''
  resetDisplay.value = true
}
</script>

<style scoped>
.calculator {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background-color: #000;
  padding: 20rpx;
  justify-content: flex-end;
}

.display {
  padding: 40rpx;
  text-align: right;
  color: white;
}

.history {
  font-size: 32rpx;
  color: #888;
  height: 40rpx;
  margin-bottom: 10rpx;
}

.current {
  font-size: 100rpx;
  font-weight: 300;
  word-break: break-all;
}

.keypad {
  display: flex;
  flex-direction: column;
  gap: 20rpx;
  padding-bottom: 40rpx;
}

.row {
  display: flex;
  gap: 20rpx;
}

.btn {
  flex: 1;
  height: 150rpx;
  border-radius: 75rpx;
  background-color: #333;
  color: white;
  font-size: 48rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  border: none;
}

.btn:active {
  opacity: 0.7;
}

.functional {
  background-color: #a5a5a5;
  color: black;
}

.operator {
  background-color: #f1a33c;
}

.zero {
  flex: 2;
  justify-content: flex-start;
  padding-left: 60rpx;
}
</style>
