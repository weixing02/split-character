<template>
  <div class="game-container">
    <HeaderSection :achievementCount="achievementCount" />

    <InputSection
      :isProcessing="isProcessing"
      @submit-plan="handlePlanSubmission"
    />

    <BattleArena
      v-if="showBattle"
      :lazyPersonality="currentBattle.lazy"
      :productivePersonality="currentBattle.productive"
      @select-personality="handlePersonalitySelection"
    />

    <CollectionDisplay
      :collection="unlockedPersonalities"
      @view-card="handleViewCard"
    />

    <CardModal
      v-if="showModal"
      :card="selectedCard"
      @close="showModal = false"
    />
  </div>
</template>

<script>
import HeaderSection from "./HeaderSection.vue";
import InputSection from "./InputSection.vue";
import BattleArena from "./BattleArena.vue";
import CollectionDisplay from "./CollectionDisplay.vue";
import CardModal from "./CardModal.vue";

export default {
  name: "GameContainer",
  components: {
    HeaderSection,
    InputSection,
    BattleArena,
    CollectionDisplay,
    CardModal,
  },
  data() {
    return {
      // 游戏状态
      isProcessing: false,
      showBattle: false,
      showModal: false,
      selectedCard: null,

      // 游戏数据
      achievementCount: 0,
      currentBattle: {
        lazy: null,
        productive: null,
      },
      unlockedPersonalities: [],

      // 人格数据库（在真实应用中，这些数据会从API获取）
      personalities: [
        {
          id: "lazy_1",
          name: "睡衣冠军",
          type: "lazy",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1516455590571-18256e5bb9ff?w=400&h=300&fit=crop",
          stats: { laziness: 90, comfort: 85, sleepQuality: 70 },
          quote: "我梦想永远不会结束的周末...",
          rarity: "common",
          evolution: "lazy_1_evolved",
        },
        {
          id: "lazy_1_evolved",
          name: "床上皇帝",
          type: "lazy",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1516455590571-18256e5bb9ff?w=400&h=300&fit=crop",
          stats: { laziness: 95, comfort: 90, sleepQuality: 85 },
          quote: "我的枕头是我的王座，我的被子是我的王国！",
          rarity: "rare",
        },
        {
          id: "productive_1",
          name: "健身狂热者",
          type: "productive",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1517836357463-d25dfeac3438?w=400&h=300&fit=crop",
          stats: { energy: 85, discipline: 80, efficiency: 75 },
          quote: "没有痛苦，就没有收获！每一秒都很重要！",
          rarity: "common",
          evolution: "productive_1_evolved",
        },
        {
          id: "productive_1_evolved",
          name: "效率机器",
          type: "productive",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1517836357463-d25dfeac3438?w=400&h=300&fit=crop",
          stats: { energy: 95, discipline: 90, efficiency: 85 },
          quote: "我已经优化了我的周末以达到最大产出！",
          rarity: "rare",
        },
        {
          id: "special_fence_sitter",
          name: "骑墙派",
          type: "special",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1516455590571-18256e5bb9ff?w=400&h=300&fit=crop",
          stats: { balance: 100, indecision: 90, flexibility: 85 },
          quote: "为什么要选择，当你可以两者兼得？",
          rarity: "epic",
        },
        {
          id: "special_time_master",
          name: "时间大师",
          type: "special",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1517836357463-d25dfeac3438?w=400&h=300&fit=crop",
          stats: { timeControl: 95, flexibility: 90, wisdom: 85 },
          quote: "我能随意支配时间 - 努力工作，玩得更嗨！",
          rarity: "legendary",
        },
      ],

      // 特殊追踪
      choices: [],
      timeTracker: {
        lastChoice: 0,
        rapidSwitches: 0,
      },
    };
  },
  created() {
    // 从localStorage加载保存的数据
    this.loadSavedData();
  },
  methods: {
    loadSavedData() {
      const savedData = localStorage.getItem("weekendPersonalitySplit");
      if (savedData) {
        const data = JSON.parse(savedData);
        this.unlockedPersonalities = data.unlocked || [];
        this.achievementCount = data.achievements?.length || 0;
      }
    },
    saveData() {
      const dataToSave = {
        unlocked: this.unlockedPersonalities,
        achievements: new Array(this.achievementCount).fill(true),
      };
      localStorage.setItem(
        "weekendPersonalitySplit",
        JSON.stringify(dataToSave)
      );
    },
    handlePlanSubmission() {
      // 在真实应用中，这里会调用AI API来生成人格
      this.isProcessing = true;

      // 模拟处理过程
      setTimeout(() => {
        // 演示用，从数据库中随机选择人格
        const lazyOptions = this.personalities.filter(
          (p) => p.type === "lazy" && p.level === 1
        );
        const productiveOptions = this.personalities.filter(
          (p) => p.type === "productive" && p.level === 1
        );

        this.currentBattle = {
          lazy: { ...lazyOptions[0] },
          productive: { ...productiveOptions[0] },
        };

        this.showBattle = true;
        this.isProcessing = false;
      }, 1500);
    },
    handlePersonalitySelection(type) {
      // 记录选择，用于特殊解锁
      this.choices.push(type);

      // 检查特殊解锁
      this.checkSpecialUnlocks(type);

      // 寻找进化形态
      const selected =
        type === "lazy"
          ? this.currentBattle.lazy
          : this.currentBattle.productive;

      if (selected.evolution) {
        const evolvedForm = this.personalities.find(
          (p) => p.id === selected.evolution
        );

        // 检查是否已解锁
        const alreadyUnlocked = this.unlockedPersonalities.some(
          (p) => p.id === evolvedForm.id
        );

        if (!alreadyUnlocked) {
          // 添加到收藏
          this.unlockedPersonalities.push(evolvedForm);
          this.selectedCard = evolvedForm;
          this.showModal = true;

          // 更新成就计数
          this.achievementCount = Math.min(
            10,
            this.unlockedPersonalities.length
          );

          // 保存到localStorage
          this.saveData();
        }
      }

      // 重置战斗状态
      setTimeout(() => {
        this.showBattle = false;
      }, 1000);
    },
    checkSpecialUnlocks() {
      // 检查"骑墙派" - 3个交替选择
      if (this.choices.length >= 3) {
        const last3 = this.choices.slice(-3);
        if (
          last3[0] !== last3[1] &&
          last3[1] !== last3[2] &&
          last3[0] !== last3[2]
        ) {
          this.unlockSpecialCard("special_fence_sitter");
        }
      }

      // 检查"时间大师" - 快速切换
      const now = Date.now();
      if (this.timeTracker.lastChoice > 0) {
        const timeDiff = now - this.timeTracker.lastChoice;
        if (timeDiff < 2000) {
          // 选择间隔少于2秒
          this.timeTracker.rapidSwitches++;
          if (this.timeTracker.rapidSwitches >= 3) {
            this.unlockSpecialCard("special_time_master");
            this.timeTracker.rapidSwitches = 0;
          }
        } else {
          this.timeTracker.rapidSwitches = 0;
        }
      }
      this.timeTracker.lastChoice = now;
    },
    unlockSpecialCard(cardId) {
      const alreadyUnlocked = this.unlockedPersonalities.some(
        (p) => p.id === cardId
      );
      if (!alreadyUnlocked) {
        const card = this.personalities.find((p) => p.id === cardId);
        if (card) {
          this.unlockedPersonalities.push(card);
          this.selectedCard = card;
          this.showModal = true;
          this.achievementCount = Math.min(
            10,
            this.unlockedPersonalities.length
          );
          this.saveData();
        }
      }
    },
    handleViewCard(card) {
      this.selectedCard = card;
      this.showModal = true;
    },
  },
};
</script>

<style scoped>
.game-container {
  max-width: 1200px;
  width: 100%;
  margin: 0 auto;
  padding: 2rem;
  position: relative;
  z-index: 1;
}

.header-section {
  text-align: center;
  margin-bottom: 1rem;
  position: relative;
}

.header-title {
  font-size: 3.5rem;
  font-weight: 700;
  margin-bottom: 1.5rem;
  color: var(--primary);
  position: relative;
  display: inline-block;
  letter-spacing: 1px;
  text-shadow: 2px 2px 0px rgba(0, 0, 0, 0.05);
  background: none;
  -webkit-text-fill-color: var(--text-primary);
}

.header-title::before {
  display: none;
}

.header-title::after {
  content: "";
  position: absolute;
  left: -15px;
  top: -15px;
  right: -15px;
  bottom: -15px;
  background-image: url("data:image/svg+xml,%3Csvg width='100' height='100' viewBox='0 0 100 100' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M11 18c3.866 0 7-3.134 7-7s-3.134-7-7-7-7 3.134-7 7 3.134 7 7 7zm48 25c3.866 0 7-3.134 7-7s-3.134-7-7-7-7 3.134-7 7 3.134 7 7 7zm-43-7c1.657 0 3-1.343 3-3s-1.343-3-3-3-3 1.343-3 3 1.343 3 3 3zm63 31c1.657 0 3-1.343 3-3s-1.343-3-3-3-3 1.343-3 3 1.343 3 3 3zM34 90c1.657 0 3-1.343 3-3s-1.343-3-3-3-3 1.343-3 3 1.343 3 3 3zm56-76c1.657 0 3-1.343 3-3s-1.343-3-3-3-3 1.343-3 3 1.343 3 3 3zM12 86c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm28-65c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm23-11c2.76 0 5-2.24 5-5s-2.24-5-5-5-5 2.24-5 5 2.24 5 5 5zm-6 60c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm29 22c2.76 0 5-2.24 5-5s-2.24-5-5-5-5 2.24-5 5 2.24 5 5 5zM32 63c2.76 0 5-2.24 5-5s-2.24-5-5-5-5 2.24-5 5 2.24 5 5 5zm57-13c2.76 0 5-2.24 5-5s-2.24-5-5-5-5 2.24-5 5 2.24 5 5 5zm-9-21c1.105 0 2-.895 2-2s-.895-2-2-2-2 .895-2 2 .895 2 2 2zM60 91c1.105 0 2-.895 2-2s-.895-2-2-2-2 .895-2 2 .895 2 2 2zM35 41c1.105 0 2-.895 2-2s-.895-2-2-2-2 .895-2 2 .895 2 2 2zM12 60c1.105 0 2-.895 2-2s-.895-2-2-2-2 .895-2 2 .895 2 2 2z' fill='%236C7D47' fill-opacity='0.1' fill-rule='evenodd'/%3E%3C/svg%3E");
  opacity: 0.3;
  z-index: -1;
  border-radius: 50%;
  transform: rotate(-5deg);
}

.header-subtitle {
  font-size: 1.2rem;
  color: var(--text-secondary);
  max-width: 600px;
  margin: 0 auto 2rem;
  line-height: 1.6;
  position: relative;
  font-family: "Century Gothic", sans-serif;
  font-style: italic;
}

.header-subtitle::after {
  content: "";
  position: absolute;
  bottom: -1rem;
  left: 50%;
  transform: translateX(-50%);
  width: 80px;
  height: 3px;
  background: var(--accent);
  border-radius: 3px;
  opacity: 0.7;
}

.input-section {
  background: var(--bg-card);
  padding: 2rem;
  border-radius: var(--border-radius-lg);
  box-shadow: var(--shadow-md);
  margin-bottom: 3rem;
  position: relative;
  overflow: hidden;
  border: 2px solid rgba(108, 125, 71, 0.3);
  background-image: url("data:image/svg+xml,%3Csvg width='40' height='40' viewBox='0 0 40 40' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='%2381B29A' fill-opacity='0.05' fill-rule='evenodd'%3E%3Cpath d='M0 38.59l2.83-2.83 1.41 1.41L1.41 40H0v-1.41zM0 1.4l2.83 2.83 1.41-1.41L1.41 0H0v1.41zM38.59 40l-2.83-2.83 1.41-1.41L40 38.59V40h-1.41zM40 1.41l-2.83 2.83-1.41-1.41L38.59 0H40v1.41zM20 18.6l2.83-2.83 1.41 1.41L21.41 20l2.83 2.83-1.41 1.41L20 21.41l-2.83 2.83-1.41-1.41L18.59 20l-2.83-2.83 1.41-1.41L20 18.59z'/%3E%3C/g%3E%3C/svg%3E");
}

.input-group {
  display: flex;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.input-field {
  flex: 1;
  padding: 1rem 1.5rem;
  background: var(--bg-light);
  border: 1px solid rgba(67, 97, 238, 0.2);
  border-radius: var(--border-radius-md);
  font-size: 1rem;
  color: var(--text-primary);
  transition: var(--transition);
}

.input-field:focus {
  border-color: var(--primary);
  box-shadow: 0 0 0 3px rgba(67, 97, 238, 0.2);
}

.generate-btn {
  background: var(--primary-gradient);
  color: var(--text-primary);
  padding: 1rem 2rem;
  border-radius: var(--border-radius-md);
  font-weight: 600;
  font-size: 1rem;
  transition: var(--transition);
  box-shadow: var(--shadow-neon);
  text-transform: uppercase;
  letter-spacing: 1px;
}

.generate-btn:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-neon), var(--shadow-lg);
}

.generate-btn:active {
  transform: translateY(0);
}

.collection-display {
  background: var(--bg-card);
  padding: 2rem;
  border-radius: var(--border-radius-lg);
  box-shadow: var(--shadow-md);
  margin-top: 3rem;
  border: 1px solid rgba(67, 97, 238, 0.2);
  position: relative;
  overflow: hidden;
}

.collection-display::before {
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

.collection-title {
  font-size: 2.5rem;
  color: var(--text-primary);
  margin-bottom: 2rem;
  font-weight: 800;
  text-transform: uppercase;
  letter-spacing: 1px;
  text-align: center;
  background: var(--primary-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.collection-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 2rem;
  position: relative;
  z-index: 1;
}

@media (max-width: 768px) {
  .game-container {
    padding: 0.1rem;
  }

  .header-title {
    font-size: 2.5rem;
  }

  .header-subtitle {
    font-size: 1rem;
  }

  .input-section,
  .collection-display {
    padding: 1.5rem;
  }

  .input-group {
    flex-direction: column;
  }

  .generate-btn {
    width: 100%;
  }

  .collection-title {
    font-size: 2rem;
  }
}
</style>
