<template>
  <section class="input-section">
    <div class="input-container">
      <div class="input-header">
        <div class="input-icon">✨</div>
        <h3 class="input-title">分享你的周末计划</h3>
        <p class="input-subtitle">让AI帮你找到最适合的周末人格！</p>
      </div>

      <div class="textarea-wrapper">
        <textarea
          v-model="planText"
          class="plan-input"
          placeholder="描述你的周末计划...(例如：'我想躺平打游戏！' 或 '我应该去撸铁吗！')"
          :disabled="isProcessing"
          @focus="handleFocus"
          @blur="handleBlur"
        ></textarea>
        <div class="textarea-decoration left"></div>
        <div class="textarea-decoration right"></div>
      </div>

      <div class="buttons-container">
        <button
          class="mic-button"
          :class="{ 'is-listening': isListening }"
          :disabled="isProcessing || !isSpeechRecognitionAvailable"
          @click="toggleVoiceInput"
          :title="isSpeechRecognitionAvailable ? '语音输入' : '语音输入不可用'"
        >
          <span class="mic-icon" v-if="!isListening">🎤</span>
          <span class="recording-icon" v-else>⚪</span>
        </button>

        <button
          class="submit-button"
          :disabled="isProcessing || !planText.trim()"
          @click="submitPlan"
        >
          <span v-if="isProcessing" class="processing">
            <span class="loader"></span>
            <span class="processing-text">正在分析你的计划...</span>
          </span>
          <span v-else class="submit-text">
            <span class="submit-icon">🔮</span>
            <span>获取我的人格！</span>
          </span>
        </button>
      </div>

      <div class="input-tips" v-if="!isProcessing">
        <div class="tip-item">
          <span class="tip-icon">💡</span>
          <span class="tip-text">提示：描述得越详细，生成的人格越有趣！</span>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: "InputSection",
  props: {
    isProcessing: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      planText: "",
      isListening: false,
      recognition: null,
      isSpeechRecognitionAvailable: false,
      isFocused: false,
    };
  },
  mounted() {
    // 检查语音识别是否可用
    if ("webkitSpeechRecognition" in window || "SpeechRecognition" in window) {
      this.isSpeechRecognitionAvailable = true;

      // 设置语音识别
      const SpeechRecognition =
        window.SpeechRecognition || window.webkitSpeechRecognition;
      this.recognition = new SpeechRecognition();
      this.recognition.continuous = false;
      this.recognition.interimResults = true;
      this.recognition.lang = "zh-CN";

      this.recognition.onresult = (event) => {
        const transcript = Array.from(event.results)
          .map((result) => result[0])
          .map((result) => result.transcript)
          .join("");

        this.planText = transcript;
      };

      this.recognition.onend = () => {
        this.isListening = false;
      };
    }
  },
  methods: {
    handleFocus() {
      this.isFocused = true;
    },
    handleBlur() {
      this.isFocused = false;
    },
    toggleVoiceInput() {
      if (this.isListening) {
        this.recognition.stop();
        this.isListening = false;
      } else {
        this.recognition.start();
        this.isListening = true;
      }
    },
    submitPlan() {
      if (this.planText.trim() && !this.isProcessing) {
        this.$emit("submit-plan", this.planText);
      }
    },
  },
};
</script>

<style scoped>
.input-section {
  background: var(--bg-card);
  border-radius: var(--border-radius-lg);
  padding: 2rem;
  margin-bottom: 3rem;
  position: relative;
  overflow: hidden;
  border: 1px solid rgba(67, 97, 238, 0.2);
  box-shadow: var(--shadow-md);
}

.input-section::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: var(--primary-gradient);
}

.input-container {
  position: relative;
  z-index: 1;
}

.input-header {
  text-align: center;
  margin-bottom: 2rem;
}

.input-icon {
  font-size: 2.5rem;
  margin-bottom: 1rem;
  animation: float 3s ease-in-out infinite;
}

.input-title {
  font-size: 2rem;
  font-weight: 900;
  margin-bottom: 0.5rem;
  background: var(--primary-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-transform: uppercase;
  letter-spacing: 2px;
}

.input-subtitle {
  font-size: 1.1rem;
  color: var(--text-secondary);
  font-weight: 500;
}

.textarea-wrapper {
  position: relative;
  margin-bottom: 2rem;
}

.plan-input {
  width: 100%;
  min-height: 150px;
  padding: 1.5rem;
  background: var(--bg-light);
  border: 1px solid rgba(67, 97, 238, 0.2);
  border-radius: var(--border-radius-lg);
  font-size: 1.1rem;
  color: var(--text-primary);
  resize: vertical;
  transition: var(--transition);
  line-height: 1.6;
  font-family: inherit;
}

.plan-input:focus {
  outline: none;
  border-color: var(--primary);
  box-shadow: 0 0 0 3px rgba(67, 97, 238, 0.2);
  transform: translateY(-2px);
}

.plan-input::placeholder {
  color: var(--text-tertiary);
}

.textarea-decoration {
  position: absolute;
  width: 60px;
  height: 60px;
  border-radius: var(--border-radius-sm);
  opacity: 0.1;
  transition: var(--transition);
  pointer-events: none;
}

.textarea-decoration.left {
  top: -20px;
  left: -20px;
  background: var(--primary);
  transform: rotate(-15deg);
}

.textarea-decoration.right {
  bottom: -20px;
  right: -20px;
  background: var(--secondary);
  transform: rotate(15deg);
}

.plan-input:focus ~ .textarea-decoration.left {
  transform: rotate(-25deg) scale(1.2);
  opacity: 0.15;
}

.plan-input:focus ~ .textarea-decoration.right {
  transform: rotate(25deg) scale(1.2);
  opacity: 0.15;
}

.buttons-container {
  display: flex;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.mic-button {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: var(--bg-light);
  border: 1px solid rgba(67, 97, 238, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5rem;
  transition: var(--transition);
  color: var(--text-primary);
}

.mic-button:hover:not(:disabled) {
  transform: translateY(-2px);
  background: var(--bg-card);
  border-color: var(--primary);
}

.mic-button.is-listening {
  background: var(--primary-gradient);
  border-color: transparent;
  animation: pulse 2s infinite;
}

.recording-icon {
  color: #ff4b4b;
  animation: blink 1s infinite;
}

.submit-button {
  flex: 1;
  height: 50px;
  background: var(--primary-gradient);
  border-radius: var(--border-radius-lg);
  color: var(--text-primary);
  font-size: 1.1rem;
  font-weight: 600;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.8rem;
  transition: var(--transition);
  text-transform: uppercase;
  letter-spacing: 1px;
  box-shadow: var(--shadow-neon);
}

.submit-button:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: var(--shadow-neon), var(--shadow-lg);
}

.submit-button:disabled {
  opacity: 0.7;
  cursor: not-allowed;
}

.processing {
  display: flex;
  align-items: center;
  gap: 0.8rem;
}

.loader {
  width: 20px;
  height: 20px;
  border: 3px solid rgba(255, 255, 255, 0.3);
  border-radius: 50%;
  border-top-color: var(--text-primary);
  animation: spin 1s linear infinite;
}

.input-tips {
  background: var(--bg-light);
  border-radius: var(--border-radius-md);
  padding: 1rem;
  border: 1px solid rgba(67, 97, 238, 0.2);
}

.tip-item {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  color: var(--text-secondary);
  font-size: 0.9rem;
}

.tip-icon {
  font-size: 1.2rem;
}

@keyframes float {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}

@keyframes pulse {
  0%,
  100% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.05);
    opacity: 0.8;
  }
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

@keyframes blink {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
}

@media (max-width: 768px) {
  .input-section {
    padding: 1.5rem;
  }

  .input-icon {
    font-size: 2rem;
  }

  .input-title {
    font-size: 1.5rem;
  }

  .input-subtitle {
    font-size: 1rem;
  }

  .plan-input {
    min-height: 120px;
    font-size: 1rem;
    padding: 1.2rem;
  }

  .buttons-container {
    flex-direction: column;
  }

  .mic-button {
    width: 100%;
    height: 45px;
  }

  .submit-button {
    height: 45px;
    font-size: 1rem;
  }
}
</style>
