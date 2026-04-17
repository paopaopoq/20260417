<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue';

// 狀態管理
const isCountdown = ref(false);
const showZhuyin = ref(true);
const isDarkMode = ref(false);

// 資料輸入
const subject = ref('');
const proctor = ref('');
const countdownMinutes = ref(null);

// 時間邏輯
const currentTime = ref(new Date());
const remainingSeconds = ref(0);
const isTimerRunning = ref(false);
let timerInterval = null;

const updateTime = () => {
  currentTime.value = new Date();
  if (isTimerRunning.value && remainingSeconds.value > 0) {
    remainingSeconds.value--;
  } else if (remainingSeconds.value === 0) {
    isTimerRunning.value = false;
  }
};

const startCountdown = () => {
  if (countdownMinutes.value > 0) {
    remainingSeconds.value = countdownMinutes.value * 60;
    isTimerRunning.value = true;
  }
};

const displayTime = computed(() => {
  if (!isCountdown.value) {
    return currentTime.value.toLocaleTimeString();
  } else {
    const mins = Math.floor(remainingSeconds.value / 60);
    const secs = remainingSeconds.value % 60;
    return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
  }
});

onMounted(() => {
  timerInterval = setInterval(updateTime, 1000);
});

onUnmounted(() => {
  clearInterval(timerInterval);
});

// 切換功能
const toggleMode = () => { isCountdown.value = !isCountdown.value; };
const toggleZhuyin = () => { showZhuyin.value = !showZhuyin.value; };
const toggleTheme = () => { isDarkMode.value = !isDarkMode.value; };
</script>

<template>
  <div :class="['app-wrapper', { 'dark-mode': isDarkMode, 'hide-zhuyin': !showZhuyin }]">
    <!-- 標題欄 -->
    <header class="header">
      <a href="https://www.et.tku.edu.tw/" target="_blank" class="nav-link">
        <ruby>淡<rt>ㄉㄢˋ</rt>江<rt>ㄐㄧㄤ</rt>教<rt>ㄐㄧㄠˋ</rt>科<rt>ㄎㄜ</rt>系<rt>ㄒㄧˋ</rt></ruby>
      </a>
      <h1 class="title">互動程式設計_413730200</h1>
    </header>

    <!-- 工具按鈕 -->
    <div class="toolbar">
      <button @click="toggleMode" class="control-btn">
        <ruby>{{ isCountdown ? '現在時間' : '倒數計時' }}<rt>{{ isCountdown ? 'ㄒㄧㄢˋㄗㄞˋㄕˊㄐㄧㄢ' : 'ㄉㄠˋㄕㄨˇㄐㄧˋㄕˊ' }}</rt></ruby>
      </button>
      <button @click="toggleZhuyin" class="control-btn">
        <ruby>{{ showZhuyin ? '隱藏注音' : '顯示注音' }}<rt>{{ showZhuyin ? 'ㄧㄣˇㄘㄤˊㄓㄨˋㄧㄣ' : 'ㄒㄧㄢˇㄕˋㄓㄨˋㄧㄣ' }}</rt></ruby>
      </button>
      <button @click="toggleTheme" class="control-btn mode-toggle">
        <ruby>{{ isDarkMode ? '白底黑字' : '黑底白字' }}<rt>{{ isDarkMode ? 'ㄅㄞˊㄉㄧˇㄏㄟㄗˋ' : 'ㄏㄟㄉㄧˇㄅㄞˊㄗˋ' }}</rt></ruby>
      </button>
    </div>

    <!-- 時間顯示區域 -->
    <main class="timer-card">
      <div class="timer-text">{{ displayTime }}</div>
      <div v-if="isCountdown" class="countdown-status">
        {{ isTimerRunning ? '倒數中...' : '請輸入時間並按下enter' }}
      </div>
    </main>

    <!-- 資料輸入與顯示區域 -->
    <div class="content-grid">
      <section class="input-panel">
        <div class="input-field">
          <label><ruby>科<rt>ㄎㄜ</rt>目<rt>ㄇㄨˋ</rt></ruby></label>
          <input v-model="subject" type="text" placeholder="例如：計算機概論">
        </div>
        <div class="input-field">
          <label><ruby>監<rt>ㄐㄧㄢ</rt>考<rt>ㄎㄠˇ</rt>老<rt>ㄌㄠˇ</rt>師<rt>ㄕ</rt></ruby></label>
          <input v-model="proctor" type="text" placeholder="請輸入老師姓名">
        </div>
        <div v-if="isCountdown" class="input-field">
          <label><ruby>倒<rt>ㄉㄠˋ</rt>數<rt>ㄕㄨˇ</rt>分<rt>ㄈㄣ</rt>鐘<rt>ㄓㄨㄥ</rt></ruby></label>
          <input v-model.number="countdownMinutes" type="number" placeholder="輸入分鐘" @keyup.enter="startCountdown">
        </div>
      </section>

      <section class="display-panel">
        <h3><ruby>當<rt>ㄉㄤ</rt>前<rt>ㄑㄧㄢˊ</rt>資<rt>ㄗ</rt>訊<rt>ㄒㄩㄣˋ</rt></ruby></h3>
        <div class="info-item">
          <span><ruby>科<rt>ㄎㄜ</rt>目<rt>ㄇㄨˋ</rt></ruby>：</span>
          <span class="val">{{ subject || '---' }}</span>
        </div>
        <div class="info-item">
          <span><ruby>監<rt>ㄐㄧㄢ</rt>考<rt>ㄎㄠˇ</rt>老<rt>ㄌㄠˇ</rt>師<rt>ㄕ</rt></ruby>：</span>
          <span class="val">{{ proctor || '---' }}</span>
        </div>
      </section>
    </div>
  </div>
</template>

<style scoped>
.app-wrapper {
  --bg-color: #f0f2f5;
  --card-bg: #ffffff;
  --text-primary: #2c3e50;
  --accent-color: #3498db;
  --btn-hover: #2980b9;
  
  min-height: 100vh;
  background-color: var(--bg-color);
  color: var(--text-primary);
  transition: all 0.4s ease;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.app-wrapper.dark-mode {
  --bg-color: #121212;
  --card-bg: #1e1e1e;
  --text-primary: #ecf0f1;
  --accent-color: #3498db;
}

.hide-zhuyin rt {
  display: none;
}

.header {
  width: 100%;
  max-width: 900px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
}

.nav-link {
  text-decoration: none;
  color: white;
  background-color: var(--accent-color);
  padding: 8px 16px;
  border-radius: 6px;
  font-weight: bold;
  transition: background 0.3s;
}

.nav-link:hover {
  background-color: var(--btn-hover);
}

.title { font-size: 1.4rem; margin: 0; }

.toolbar {
  display: flex;
  gap: 12px;
  margin-bottom: 2rem;
  flex-wrap: wrap;
  justify-content: center;
}

.control-btn {
  background: var(--card-bg);
  color: var(--text-primary);
  border: 1px solid #ddd;
  padding: 10px 20px;
  border-radius: 30px;
  cursor: pointer;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
  transition: all 0.2s;
}

.control-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.15);
}

.timer-card {
  background-color: #1a1a1a;
  color: #00ff00;
  padding: 1.5rem;
  border-radius: 15px;
  width: 100%;
  max-width: 600px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 200px; /* 固定最小高度，防止內容切換時跳動 */
  box-shadow: 0 10px 30px rgba(0,0,0,0.3);
  margin-bottom: 2rem;
}

.timer-text {
  font-size: clamp(3rem, 12vw, 5.5rem); /* 自動縮放字體：最小 3rem，隨寬度變化，最大 5.5rem */
  font-family: 'Courier New', monospace;
  font-weight: bold;
  line-height: 1.1;
  white-space: nowrap; /* 防止時間自動斷行 */
  margin: 0;
}

.content-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  width: 100%;
  max-width: 800px;
}

.input-panel, .display-panel {
  background: var(--card-bg);
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0,0,0,0.05);
}

.input-field { margin-bottom: 1rem; }
.input-field label { display: block; margin-bottom: 5px; opacity: 0.8; }
.input-field input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: var(--bg-color);
  color: var(--text-primary);
}

@media (max-width: 768px) {
  .content-grid { grid-template-columns: 1fr; }
  .header { flex-direction: column; gap: 15px; }
}
</style>
