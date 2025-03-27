<template>
  <div id="app">
    <GameContainer />
  </div>
</template>

<script>
import GameContainer from "./components/GameContainer.vue";

export default {
  name: "App",
  components: {
    GameContainer,
  },
};
</script>

<style>
:root {
  /* 主色调 */
  --primary: #4361ee;
  --primary-dark: #3a0ca3;
  --secondary: #00f5d4;
  --accent: #fee440;

  /* 渐变 */
  --primary-gradient: linear-gradient(135deg, #4361ee, #3a0ca3);
  --secondary-gradient: linear-gradient(135deg, #00f5d4, #00b4d8);
  --accent-gradient: linear-gradient(135deg, #fee440, #f8961e);

  /* 背景色 */
  --bg-dark: #1a1b1e;
  --bg-card: #252729;
  --bg-light: #2a2b2e;

  /* 文字颜色 */
  --text-primary: #ffffff;
  --text-secondary: rgba(255, 255, 255, 0.7);
  --text-tertiary: rgba(255, 255, 255, 0.5);

  /* 阴影 */
  --shadow-sm: 0 2px 8px rgba(0, 0, 0, 0.2);
  --shadow-md: 0 4px 16px rgba(0, 0, 0, 0.3);
  --shadow-lg: 0 8px 24px rgba(0, 0, 0, 0.4);
  --shadow-neon: 0 0 20px rgba(67, 97, 238, 0.3);

  /* 圆角 */
  --border-radius-sm: 8px;
  --border-radius-md: 12px;
  --border-radius-lg: 20px;

  /* 动画 */
  --transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  --transition-bounce: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    "Helvetica Neue", Arial, sans-serif;
  background: var(--bg-dark);
  color: var(--text-primary);
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem;
  background: radial-gradient(
      circle at 10% 20%,
      rgba(67, 97, 238, 0.1) 0%,
      transparent 50%
    ),
    radial-gradient(
      circle at 90% 80%,
      rgba(0, 245, 212, 0.1) 0%,
      transparent 50%
    ),
    var(--bg-dark);
  position: relative;
  overflow: hidden;
}

#app::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(67, 97, 238, 0.2),
    transparent
  );
}

#app::after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(0, 245, 212, 0.2),
    transparent
  );
}

/* 滚动条样式 */
::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}

::-webkit-scrollbar-track {
  background: var(--bg-light);
  border-radius: 3px;
}

::-webkit-scrollbar-thumb {
  background: var(--primary);
  border-radius: 3px;
  transition: var(--transition);
}

::-webkit-scrollbar-thumb:hover {
  background: var(--primary-dark);
}

/* 按钮基础样式 */
button {
  cursor: pointer;
  border: none;
  background: none;
  font-family: inherit;
  font-size: inherit;
  color: inherit;
  padding: 0;
  transition: var(--transition);
}

button:focus {
  outline: none;
}

button.primary {
  background: var(--primary-gradient);
  color: var(--text-primary);
  padding: 0.8rem 1.5rem;
  border-radius: var(--border-radius-md);
  font-weight: 600;
  box-shadow: var(--shadow-neon);
}

button.primary:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-neon), var(--shadow-lg);
}

/* 输入框基础样式 */
input {
  font-family: inherit;
  font-size: inherit;
  color: var(--text-primary);
  background: var(--bg-light);
  border: 1px solid rgba(67, 97, 238, 0.2);
  border-radius: var(--border-radius-md);
  padding: 0.8rem 1.2rem;
  transition: var(--transition);
}

input:focus {
  outline: none;
  border-color: var(--primary);
  box-shadow: 0 0 0 3px rgba(67, 97, 238, 0.2);
}

/* 动画类 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.slide-up-enter-active,
.slide-up-leave-active {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.slide-up-enter-from,
.slide-up-leave-to {
  transform: translateY(20px);
  opacity: 0;
}

/* 响应式设计 */
@media (max-width: 768px) {
  #app {
    padding: 1rem;
  }
}
</style>
