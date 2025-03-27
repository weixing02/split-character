<template>
  <div class="modal-overlay" @click.self="closeModal">
    <div class="card-modal" :class="['rarity-' + card.rarity]">
      <button class="close-button" @click="closeModal">×</button>

      <div class="card-header">
        <div class="rarity-badge">{{ formatRarity(card.rarity) }}</div>
        <h2 class="card-title">{{ card.name }}</h2>
        <div class="card-type">{{ formatType(card.type) }}角色</div>
      </div>

      <div class="card-animation">
        <img :src="card.image" :alt="card.name" class="character-img" />
      </div>

      <div class="card-stats">
        <div class="stat-item" v-for="(value, stat) in card.stats" :key="stat">
          <div class="stat-name">{{ formatStatName(stat) }}</div>
          <div class="stat-bar-container">
            <div class="stat-bar" :style="{ width: `${value}%` }"></div>
            <div class="stat-value">{{ value }}</div>
          </div>
        </div>
      </div>

      <div class="card-quote">
        <q>{{ card.quote }}</q>
      </div>

      <div class="card-level" v-if="card.level > 1">
        <div class="level-stars">
          <span class="star" v-for="n in card.level" :key="n">★</span>
        </div>
        <div class="level-text">等级 {{ card.level }}</div>
      </div>

      <div class="unlock-message" v-if="isNewUnlock">
        <div class="confetti-animation">🎉</div>
        <div class="unlock-text">新人格已解锁！</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "CardModal",
  props: {
    card: {
      type: Object,
      required: true,
    },
    isNewUnlock: {
      type: Boolean,
      default: true,
    },
  },
  methods: {
    formatRarity(rarity) {
      const rarityMap = {
        common: "普通",
        rare: "稀有",
        epic: "史诗",
        legendary: "传说",
      };
      return (
        rarityMap[rarity] || rarity.charAt(0).toUpperCase() + rarity.slice(1)
      );
    },
    formatType(type) {
      if (type === "lazy") return "摆烂";
      if (type === "productive") return "卷王";
      if (type === "special") return "特殊";
      return type.charAt(0).toUpperCase() + type.slice(1);
    },
    formatStatName(stat) {
      const statMap = {
        laziness: "摆烂指数",
        comfort: "舒适度",
        sleepQuality: "睡眠质量",
        energy: "能量值",
        discipline: "自律值",
        efficiency: "效率值",
        balance: "平衡值",
        indecision: "犹豫值",
        flexibility: "灵活度",
        timeControl: "时间掌控",
        wisdom: "智慧值",
      };
      return (
        statMap[stat] ||
        stat.charAt(0).toUpperCase() + stat.slice(1).replace(/([A-Z])/g, " $1")
      );
    },
    closeModal() {
      this.$emit("close");
    },
  },
};
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.8);
  backdrop-filter: blur(10px);
  display: flex;
  align-items: flex-start;
  justify-content: center;
  z-index: 100;
  padding: 1rem;
  overflow-y: auto;
}

.card-modal {
  max-height: 96vh;
  overflow-y: auto;
  background: var(--bg-card);
  border-radius: var(--border-radius-lg);
  padding: 2rem;
  width: 90%;
  max-width: 500px;
  position: relative;
  animation: slide-up 0.3s ease;
  box-shadow: var(--shadow-lg), 0 0 40px rgba(67, 97, 238, 0.2);
  border: 1px solid rgba(67, 97, 238, 0.2);
}

.card-modal::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: radial-gradient(
      circle at 0% 0%,
      rgba(67, 97, 238, 0.1) 0%,
      transparent 50%
    ),
    radial-gradient(
      circle at 100% 100%,
      rgba(0, 245, 212, 0.1) 0%,
      transparent 50%
    );
  pointer-events: none;
}

.close-button {
  position: absolute;
  top: 1rem;
  right: 1rem;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.1);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: var(--transition);
  color: var(--text-primary);
  font-size: 1.5rem;
  z-index: 2;
}

.close-button:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: rotate(90deg);
}

.card-header {
  text-align: center;
  margin-bottom: 2rem;
  position: relative;
}

.rarity-badge {
  display: inline-block;
  padding: 0.5rem 1.5rem;
  background: var(--primary-gradient);
  color: var(--text-primary);
  border-radius: 20px;
  font-size: 0.9rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 1px;
  margin-bottom: 1rem;
  box-shadow: var(--shadow-neon);
}

.card-title {
  font-size: 2rem;
  font-weight: 900;
  margin-bottom: 1rem;
  background: var(--primary-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-transform: uppercase;
  letter-spacing: 2px;
  position: relative;
  display: inline-block;
}

.card-title::before {
  content: attr(data-text);
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background: var(--accent-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  opacity: 0.5;
  transform: translateX(-0.5rem);
  z-index: -1;
}

.card-type {
  display: inline-block;
  padding: 0.5rem 1.5rem;
  background: var(--bg-light);
  color: var(--text-secondary);
  border-radius: var(--border-radius-md);
  font-size: 0.9rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 1px;
  border: 1px solid rgba(67, 97, 238, 0.2);
}

.card-animation {
  height: 250px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--bg-dark);
  border-radius: var(--border-radius-lg);
  margin: 2rem 0;
  position: relative;
  overflow: hidden;
  border: 1px solid rgba(67, 97, 238, 0.2);
}

.card-animation::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: radial-gradient(
    circle at center,
    rgba(67, 97, 238, 0.1) 0%,
    transparent 70%
  );
  pointer-events: none;
}

.character-img {
  width: 200px;
  height: 200px;
  object-fit: cover;
  border-radius: 50%;
  border: 3px solid var(--primary);
  box-shadow: 0 0 30px rgba(67, 97, 238, 0.3);
  transition: var(--transition);
}

.character-img:hover {
  transform: scale(1.05);
  box-shadow: 0 0 40px rgba(67, 97, 238, 0.4);
}

.card-stats {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  margin: 2rem 0;
}

.stat-item {
  background: var(--bg-light);
  padding: 1rem;
  border-radius: var(--border-radius-md);
  border: 1px solid rgba(67, 97, 238, 0.2);
}

.stat-name {
  font-size: 0.9rem;
  color: var(--text-secondary);
  margin-bottom: 0.5rem;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.stat-bar-container {
  height: 6px;
  background: var(--bg-dark);
  border-radius: 3px;
  overflow: hidden;
  margin-bottom: 0.5rem;
}

.stat-bar {
  height: 100%;
  background: var(--primary-gradient);
  border-radius: 3px;
  transition: width 1s ease;
}

.stat-value {
  font-size: 1.2rem;
  font-weight: 700;
  color: var(--text-primary);
  text-align: right;
}

.card-quote {
  font-size: 1.1rem;
  color: var(--text-secondary);
  font-style: italic;
  text-align: center;
  margin: 2rem 0;
  padding: 1.5rem;
  background: var(--bg-light);
  border-radius: var(--border-radius-lg);
  position: relative;
  border: 1px solid rgba(67, 97, 238, 0.2);
}

.card-quote::before {
  content: '"';
  position: absolute;
  top: 1rem;
  left: 1rem;
  font-size: 4rem;
  font-family: serif;
  opacity: 0.1;
  color: var(--primary);
}

.card-level {
  text-align: center;
  margin-top: 2rem;
}

.level-stars {
  color: var(--accent);
  font-size: 1.5rem;
  margin-bottom: 0.5rem;
  text-shadow: 0 0 10px rgba(254, 228, 64, 0.5);
}

.level-text {
  font-size: 1rem;
  font-weight: 600;
  color: var(--text-secondary);
  text-transform: uppercase;
  letter-spacing: 1px;
}

.unlock-message {
  text-align: center;
  margin-top: 2rem;
  padding: 1rem;
  background: var(--bg-light);
  border-radius: var(--border-radius-lg);
  border: 1px solid rgba(67, 97, 238, 0.2);
  animation: pulse 2s infinite;
}

.confetti-animation {
  font-size: 2rem;
  margin-bottom: 0.5rem;
  animation: bounce 2s infinite;
}

.unlock-text {
  font-size: 1.2rem;
  color: var(--text-primary);
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 1px;
}

/* 稀有度样式 */
.rarity-common {
  box-shadow: var(--shadow-lg);
}

.rarity-rare {
  box-shadow: var(--shadow-lg), 0 0 40px rgba(67, 97, 238, 0.3);
}

.rarity-rare .card-header {
  background: linear-gradient(135deg, rgba(67, 97, 238, 0.1), transparent);
}

.rarity-epic {
  box-shadow: var(--shadow-lg), 0 0 40px rgba(139, 92, 246, 0.3);
}

.rarity-epic .card-header {
  background: linear-gradient(135deg, rgba(139, 92, 246, 0.1), transparent);
}

.rarity-legendary {
  box-shadow: var(--shadow-lg), 0 0 40px rgba(254, 228, 64, 0.3);
}

.rarity-legendary .card-header {
  background: linear-gradient(135deg, rgba(254, 228, 64, 0.1), transparent);
}

@keyframes fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slide-up {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes bounce {
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
    opacity: 0.8;
    transform: scale(1);
  }
  50% {
    opacity: 1;
    transform: scale(1.05);
  }
}

@media (max-width: 768px) {
  .card-modal {
    padding: 1.5rem;
    width: 95%;
  }

  .card-title {
    font-size: 1.6rem;
  }

  .card-animation {
    height: 200px;
  }

  .character-img {
    width: 120px;
    height: 120px;
  }

  .card-stats {
    grid-template-columns: 1fr;
  }

  .card-quote {
    font-size: 1rem;
    padding: 1rem;
  }

  .level-stars {
    font-size: 1.2rem;
  }
}

@media (max-width: 480px) {
  .card-modal {
    padding: 1.2rem 0.8rem;
  }

  .card-title {
    font-size: 1.4rem;
  }

  .card-type {
    font-size: 0.8rem;
    padding: 0.3rem 0.6rem;
  }

  .card-stats {
    gap: 0.6rem;
  }

  .stat-name {
    font-size: 0.7rem;
  }

  .stat-value {
    font-size: 0.9rem;
  }
}
</style>
