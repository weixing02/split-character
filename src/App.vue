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
/* 全局样式 */
body {
  font-family: "Arial Rounded MT Bold", "Nunito", sans-serif;
  margin: 0;
  padding: 0;
  background-color: var(--bg-main);
  color: var(--text-primary);
  background-image: url("data:image/svg+xml,%3Csvg width='52' height='26' viewBox='0 0 52 26' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%23a1d9c1' fill-opacity='0.1'%3E%3Cpath d='M10 10c0-2.21-1.79-4-4-4-3.314 0-6-2.686-6-6h2c0 2.21 1.79 4 4 4 3.314 0 6 2.686 6 6 0 2.21 1.79 4 4 4 3.314 0 6 2.686 6 6 0 2.21 1.79 4 4 4v2c-3.314 0-6-2.686-6-6 0-2.21-1.79-4-4-4-3.314 0-6-2.686-6-6zm25.464-1.95l8.486 8.486-1.414 1.414-8.486-8.486 1.414-1.414z' /%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
}

:root {
  /* 颜色变量 */
  --primary: #4361ee;
  --primary-dark: #3a56d4;
  --secondary: #f15bb5;
  --accent: #00bbf9;
  --bg-main: #f8f9fa;
  --bg-card: #ffffff;
  --bg-light: #f1f3f9;
  --text-primary: #333;
  --text-secondary: #718096;

  /* 渐变 */
  --primary-gradient: linear-gradient(135deg, #4361ee, #3a56d4);
  --accent-gradient: linear-gradient(135deg, #00bbf9, #4361ee);
  --success-gradient: linear-gradient(135deg, #4ade80, #22c55e);
  --warning-gradient: linear-gradient(135deg, #fbbf24, #f59e0b);
  --danger-gradient: linear-gradient(135deg, #f87171, #ef4444);

  /* 边角和阴影 */
  --border-radius-sm: 0.25rem;
  --border-radius-md: 0.5rem;
  --border-radius-lg: 1rem;
  --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.1);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);

  /* 动画 */
  --transition: all 0.3s ease;
}

/* ... existing code ... */

/* 全局样式 */
body {
  font-family: "Arial Rounded MT Bold", "Nunito", sans-serif;
  margin: 0;
  padding: 0;
  background-color: var(--bg-main);
  color: var(--text-primary);
  background-image: url("data:image/svg+xml,%3Csvg width='52' height='26' viewBox='0 0 52 26' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%23a1d9c1' fill-opacity='0.1'%3E%3Cpath d='M10 10c0-2.21-1.79-4-4-4-3.314 0-6-2.686-6-6h2c0 2.21 1.79 4 4 4 3.314 0 6 2.686 6 6 0 2.21 1.79 4 4 4 3.314 0 6 2.686 6 6 0 2.21 1.79 4 4 4v2c-3.314 0-6-2.686-6-6 0-2.21-1.79-4-4-4-3.314 0-6-2.686-6-6zm25.464-1.95l8.486 8.486-1.414 1.414-8.486-8.486 1.414-1.414z' /%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
}

:root {
  /* 吉卜力风格颜色变量 */
  --primary: #6c7d47; /* 灰绿色 - 自然森林 */
  --primary-dark: #567447; /* 深绿色 - 龙猫色调 */
  --secondary: #e07a5f; /* 暖红色 - 天空之城 */
  --accent: #81b29a; /* 薄荷绿 - 绿意盎然 */
  --bg-main: #f9f1e6; /* 米色纸张 - 温暖背景 */
  --bg-card: #faf6ed; /* 浅米色 - 卡片背景 */
  --bg-light: #f2e9de; /* 更浅的米色 - 轻质纸张 */
  --text-primary: #3d405b; /* 深蓝灰 - 主文字 */
  --text-secondary: #787a91; /* 浅灰蓝 - 次要文字 */

  /* 渐变 */
  --primary-gradient: linear-gradient(135deg, #6c7d47, #567447);
  --accent-gradient: linear-gradient(135deg, #81b29a, #6c7d47);
  --success-gradient: linear-gradient(135deg, #81b29a, #6c7d47);
  --warning-gradient: linear-gradient(135deg, #f2cc8f, #e07a5f);
  --danger-gradient: linear-gradient(135deg, #e07a5f, #d56c50);

  /* 柔和圆角和轻微阴影 - 吉卜力风格更圆润自然 */
  --border-radius-sm: 0.5rem;
  --border-radius-md: 0.75rem;
  --border-radius-lg: 1.5rem;
  --shadow-sm: 0 2px 4px rgba(61, 64, 91, 0.1);
  --shadow-md: 0 4px 8px rgba(61, 64, 91, 0.1);
  --shadow-lg: 0 6px 16px rgba(61, 64, 91, 0.1);

  /* 柔和过渡 */
  --transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
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
  padding: 0.2rem;
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
    padding: 0.4rem;
  }
}
</style>
