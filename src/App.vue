<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'

// 狀態管理
const isDarkMode = ref(false)
const showZhuyin = ref(false)
const mode = ref('clock') // 'clock' 或 'countdown'

// 時間相關
const currentTime = ref(new Date())
const countdownSeconds = ref(600) // 預設 10 分鐘
let timerInterval = null

// 表單資料
const examInfo = ref({
  time: '',
  subject: '',
  teacher: ''
})
const submittedInfo = ref(null)

// 更新時間與倒數
onMounted(() => {
  timerInterval = setInterval(() => {
    currentTime.value = new Date()
    if (mode.value === 'countdown' && countdownSeconds.value > 0) {
      countdownSeconds.value--
    }
  }, 1000)
})

onUnmounted(() => {
  clearInterval(timerInterval)
})

// 格式化顯示
const displayTime = computed(() => {
  if (mode.value === 'clock') {
    return currentTime.value.toLocaleTimeString('zh-TW', { hour12: false })
  } else {
    const mins = Math.floor(countdownSeconds.value / 60)
    const secs = countdownSeconds.value % 60
    return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`
  }
})

const toggleMode = () => {
  mode.value = mode.value === 'clock' ? 'countdown' : 'clock'
}

const handleSubmit = () => {
  submittedInfo.value = { ...examInfo.value }
}

// 輔助函式：處理注音標籤
// 這裡手動為標題與重要文字加上注音範例
const zhuyinMap = {
  '互動程式設計': 'ㄏㄨˋㄉㄨㄥˋㄔㄥˊㄕˋㄕㄜˋㄐㄧˋ',
  '現在時間': 'ㄒㄧㄢˋㄗㄞˋㄕˊㄐㄧㄢ',
  '倒數計時': 'ㄉㄠˇㄕㄨˋㄐㄧˋㄕˊ',
  '切換模式': 'ㄑㄧㄝㄏㄨㄢˋㄇㄛˊㄕˋ',
  '注音開關': 'ㄓㄨˋㄧㄣㄎㄞㄍㄨㄢ',
  '色彩模式': 'ㄙㄜˋㄘㄞˇㄇㄛˊㄕˋ',
  '監考資訊': 'ㄐㄧㄢㄎㄠˇㄗ資ㄒㄩㄣˋ'
}
</script>

<template>
  <div :class="['app-container', isDarkMode ? 'dark-mode' : 'light-mode']">
    <!-- 標題欄位 -->
    <header class="header">
      <a href="https://www.et.tku.edu.tw/" target="_blank" class="nav-btn">
        <ruby>淡江教科系<rt v-if="showZhuyin">ㄉㄢˋㄐㄧㄤㄐㄧㄠㄎㄜㄒㄧˋ</rt></ruby>
      </a>
      <h1 class="title">
        <ruby>互動程式設計_413730234<rt v-if="showZhuyin">ㄏㄨˋㄉㄨㄥˋㄔㄥˊㄕˋㄕㄜˋㄐㄧˋ_413730234</rt></ruby>
      </h1>
      <div class="placeholder"></div>
    </header>

    <main class="content">
      <!-- 功能按鈕區 -->
      <div class="toolbar">
        <button @click="toggleMode">
          <ruby>{{ mode === 'clock' ? '切換倒數' : '切換時間' }}<rt v-if="showZhuyin">{{ mode === 'clock' ? 'ㄑㄧㄝㄏㄨㄢˋㄉㄠˇㄕㄨˋ' : 'ㄑㄧㄝㄏㄨㄢˋㄕˊㄐㄧㄢ' }}</rt></ruby>
        </button>
        <button @click="showZhuyin = !showZhuyin">
          <ruby>注音切換<rt v-if="showZhuyin">ㄓㄨˋㄧㄣㄑㄧㄝㄏㄨㄢˋ</rt></ruby>
        </button>
        <button @click="isDarkMode = !isDarkMode">
          <ruby>黑白切換<rt v-if="showZhuyin">ㄏㄟㄅㄞˊㄑㄧㄝㄏㄨㄢˋ</rt></ruby>
        </button>
      </div>

      <!-- 時間顯示區 -->
      <div class="time-display">
        <h2 class="mode-label">{{ mode === 'clock' ? '現在時間' : '剩餘時間' }}</h2>
        <div class="time-text">{{ displayTime }}</div>
      </div>

      <!-- 資料輸入區 -->
      <section class="form-section">
        <h3><ruby>監考資訊輸入<rt v-if="showZhuyin">ㄐㄧㄢㄎㄠˇㄗ資ㄒㄩㄣˋㄕㄨㄖㄨˋ</rt></ruby></h3>
        <div class="input-group">
          <input v-model="examInfo.time" placeholder="考試時間 (如: 10:00 - 12:00)" />
          <input v-model="examInfo.subject" placeholder="考試科目" />
          <input v-model="examInfo.teacher" placeholder="監考老師" />
          <button @click="handleSubmit">確認送出</button>
        </div>
      </section>

      <!-- 顯示輸入結果 -->
      <section v-if="submittedInfo" class="display-section">
        <div class="info-card">
          <p><strong>科目：</strong> {{ submittedInfo.subject }}</p>
          <p><strong>時間：</strong> {{ submittedInfo.time }}</p>
          <p><strong>監考老師：</strong> {{ submittedInfo.teacher }}</p>
        </div>
      </section>
    </main>
  </div>
</template>

<style>
/* 全域基礎樣式 */
body {
  margin: 0;
  padding: 0;
  font-family: 'PingFang TC', 'Microsoft JhengHei', sans-serif;
  transition: background-color 0.3s, color 0.3s;
}

rt {
  font-size: 0.4em;
  color: inherit;
}
</style>

<style scoped>
.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}

/* 響應式色彩 */
.light-mode {
  background-color: #ffffff;
  color: #000000;
}

.dark-mode {
  background-color: #000000;
  color: #ffffff;
}

.dark-mode .nav-btn, .dark-mode button, .dark-mode input {
  border-color: #ffffff;
  color: #ffffff;
  background: transparent;
}

/* 標題欄 */
.header {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  border-bottom: 1px solid #ccc;
  flex-wrap: wrap;
}

.title {
  font-size: 1.5rem;
  margin: 0.5rem;
  text-align: center;
}

.nav-btn {
  text-decoration: none;
  padding: 8px 16px;
  border: 1px solid #000;
  border-radius: 4px;
  font-size: 0.9rem;
}

.placeholder { width: 100px; } /* 用於平衡 Flexbox */

/* 功能區域 */
.content {
  max-width: 800px;
  width: 90%;
  padding: 2rem 0;
  text-align: center;
}

.toolbar {
  display: flex;
  gap: 10px;
  justify-content: center;
  margin-bottom: 2rem;
  flex-wrap: wrap;
}

button {
  padding: 10px 20px;
  cursor: pointer;
  border: 1px solid #000;
  background: #f0f0f0;
  border-radius: 5px;
  transition: 0.2s;
}

.time-display {
  margin: 2rem 0;
  padding: 2rem;
  border: 2px dashed #888;
  border-radius: 15px;
}

.time-text {
  font-size: 4rem;
  font-weight: bold;
  font-family: monospace;
}

.form-section {
  margin-top: 2rem;
  text-align: left;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

input {
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
}

.display-section {
  margin-top: 20px;
}

.info-card {
  padding: 20px;
  border: 1px solid #888;
  border-radius: 8px;
  text-align: left;
  line-height: 1.6;
}

/* 手機版調整 */
@media (max-width: 600px) {
  .header { justify-content: center; flex-direction: column; }
  .time-text { font-size: 2.5rem; }
  .placeholder { display: none; }
}
</style>
