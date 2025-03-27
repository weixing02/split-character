<template>
  <header class="header-section">
    <h1 class="title">split!!</h1>
  </header>
</template>

<script>
export default {
  name: "HeaderSection",
  props: {
    achievementCount: {
      type: Number,
      default: 0,
    },
  },
};
</script>

<style scoped>
.header-section {
  text-align: center;
  margin-bottom: 0.5rem;
  padding: 2rem;
  background: var(--card-bg);
  border-radius: 20px;
  box-shadow: 0 4px 15px var(--shadow-color);
  border: 2px solid rgba(255, 255, 255, 0.3);
  position: relative;
  overflow: hidden;
}

.header-section::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 6px;
  background: var(--gradient-1);
  opacity: 0.8;
}

h1 {
  font-size: 3rem;
  color: var(--text-color);
  margin-bottom: 1rem;
  text-shadow: 2px 2px 4px var(--shadow-color);
  animation: float 3s infinite;
  position: relative;
  display: inline-block;
}

h1::after {
  content: "✨";
  position: absolute;
  right: -30px;
  top: -10px;
  font-size: 1.5rem;
  animation: wiggle 1s infinite;
}

.subtitle {
  font-size: 1.4rem;
  color: #666;
  margin-bottom: 2rem;
  opacity: 0.8;
  position: relative;
}

.achievement-counter {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 1.5rem;
  margin-top: 1.5rem;
  background: rgba(255, 255, 255, 0.5);
  padding: 1rem;
  border-radius: 15px;
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.achievement-label {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  font-weight: bold;
  font-size: 1.2rem;
}

.achievement-icon {
  font-size: 2rem;
  animation: bounce 2s infinite;
}

.progress-container {
  flex: 1;
  height: 12px;
  background-color: rgba(255, 255, 255, 0.3);
  border-radius: 6px;
  overflow: hidden;
  max-width: 300px;
  border: 2px solid rgba(255, 255, 255, 0.5);
}

.progress-bar {
  height: 100%;
  background: var(--gradient-2);
  border-radius: 6px;
  transition: width 0.5s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  overflow: hidden;
}

.progress-bar::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.3),
    transparent
  );
  animation: shimmer 2s infinite;
}

@keyframes shimmer {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(100%);
  }
}

.achievement-count {
  display: inline-block;
  background: var(--gradient-1);
  color: white;
  padding: 0.6rem 1.2rem;
  border-radius: 25px;
  font-weight: bold;
  box-shadow: 0 2px 8px var(--shadow-color);
  animation: bounce 2s infinite;
  font-size: 1.2rem;
}

@media (max-width: 768px) {
  .header-section {
    padding: 0.3rem 1rem;
  }

  h1 {
    font-size: 2.2rem;
  }

  h1::after {
    right: -20px;
    top: -5px;
    font-size: 1.2rem;
  }

  .subtitle {
    font-size: 1.1rem;
  }

  .achievement-counter {
    padding: 0.8rem;
    gap: 1rem;
  }

  .achievement-label {
    font-size: 1rem;
  }

  .achievement-icon {
    font-size: 1.5rem;
  }

  .progress-container {
    max-width: 200px;
  }

  .achievement-count {
    padding: 0.5rem 1rem;
    font-size: 1rem;
  }
}
</style>
