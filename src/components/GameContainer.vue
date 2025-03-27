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
        // 摆烂方基础形态
        {
          id: "lazy_1",
          name: "睡衣冠军",
          type: "lazy",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1520367288098-6be20147fff7?w=400&h=300&fit=crop",
          stats: { laziness: 90, comfort: 85, sleepQuality: 70 },
          quote: "我梦想永远不会结束的周末...",
          rarity: "common",
          evolution: "lazy_1_evolved",
        },
        {
          id: "lazy_2",
          name: "网瘾少年",
          type: "lazy",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1593305841991-05c297ba4575?w=400&h=300&fit=crop",
          stats: { gaming: 88, reaction: 85, entertainment: 75 },
          quote: "这把赢了我就去睡觉... 好吧，最后一把！",
          rarity: "common",
          evolution: "lazy_2_evolved",
        },
        {
          id: "lazy_3",
          name: "美食达人",
          type: "lazy",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1565299624946-b28f40a0ae38?w=400&h=300&fit=crop",
          stats: { taste: 90, cooking: 70, enjoyment: 85 },
          quote: "人生最大的乐趣就是吃喝玩乐~",
          rarity: "common",
          evolution: "lazy_3_evolved",
        },
        {
          id: "lazy_4",
          name: "追剧狂魔",
          type: "lazy",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1522869635100-9f4c5e86aa37?w=400&h=300&fit=crop",
          stats: { binge: 92, focus: 75, analysis: 80 },
          quote: "这部剧太好看了，不能停！",
          rarity: "common",
          evolution: "lazy_4_evolved",
        },
        {
          id: "lazy_5",
          name: "社交达人",
          type: "lazy",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1529156069898-49953e39b3ac?w=400&h=300&fit=crop",
          stats: { charm: 85, communication: 80, fun: 90 },
          quote: "周末就是要和朋友一起快乐玩耍！",
          rarity: "common",
          evolution: "lazy_5_evolved",
        },

        // 摆烂方进化形态
        {
          id: "lazy_1_evolved",
          name: "床上皇帝",
          type: "lazy",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1605602660977-0bd0d1a3adf5?w=400&h=300&fit=crop",
          stats: { laziness: 95, comfort: 90, sleepQuality: 85 },
          quote: "我的枕头是我的王座，我的被子是我的王国！",
          rarity: "rare",
        },
        {
          id: "lazy_2_evolved",
          name: "电竞王者",
          type: "lazy",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1542751371-adc38448a05e?w=400&h=300&fit=crop",
          stats: { gaming: 95, reaction: 92, entertainment: 88 },
          quote: "这把我能1v5，躺赢不是梦！",
          rarity: "rare",
        },
        {
          id: "lazy_3_evolved",
          name: "美食评论家",
          type: "lazy",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1414235077428-338989a2e8c0?w=400&h=300&fit=crop",
          stats: { taste: 95, cooking: 85, enjoyment: 90 },
          quote: "我不是在吃，就是在寻找美食的路上~",
          rarity: "rare",
        },
        {
          id: "lazy_4_evolved",
          name: "剧评大师",
          type: "lazy",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1586899028174-e7098604235b?w=400&h=300&fit=crop",
          stats: { binge: 95, focus: 85, analysis: 90 },
          quote: "我能预测剧情，但我就是要看下去！",
          rarity: "rare",
        },
        {
          id: "lazy_5_evolved",
          name: "派对之王",
          type: "lazy",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1533174072545-7a4b6ad7a6c3?w=400&h=300&fit=crop",
          stats: { charm: 92, communication: 88, fun: 95 },
          quote: "生活就该及时行乐，开心就好！",
          rarity: "rare",
        },

        // 卷王方基础形态
        {
          id: "productive_1",
          name: "健身狂热者",
          type: "productive",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1526506118085-60ce8714f8c5?w=400&h=300&fit=crop",
          stats: { energy: 85, discipline: 80, efficiency: 75 },
          quote: "没有痛苦，就没有收获！每一秒都很重要！",
          rarity: "common",
          evolution: "productive_1_evolved",
        },
        {
          id: "productive_2",
          name: "读书学霸",
          type: "productive",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1491841550275-ad7854e35ca6?w=400&h=300&fit=crop",
          stats: { knowledge: 88, memory: 85, comprehension: 80 },
          quote: "周末是充实自己的最好时机！",
          rarity: "common",
          evolution: "productive_2_evolved",
        },
        {
          id: "productive_3",
          name: "创业达人",
          type: "productive",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1521737604893-d14cc237f11d?w=400&h=300&fit=crop",
          stats: { business: 85, innovation: 80, planning: 82 },
          quote: "机会是留给有准备的人的！",
          rarity: "common",
          evolution: "productive_3_evolved",
        },
        {
          id: "productive_4",
          name: "艺术家",
          type: "productive",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1460661419201-fd4cecdf8a8b?w=400&h=300&fit=crop",
          stats: { creativity: 90, expression: 85, skill: 75 },
          quote: "艺术创作是最好的周末消遣！",
          rarity: "common",
          evolution: "productive_4_evolved",
        },
        {
          id: "productive_5",
          name: "运动健将",
          type: "productive",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1517963879433-6ad2b056d712?w=400&h=300&fit=crop",
          stats: { strength: 85, agility: 80, endurance: 82 },
          quote: "周末就要活力满满，动起来！",
          rarity: "common",
          evolution: "productive_5_evolved",
        },

        // 卷王方进化形态
        {
          id: "productive_1_evolved",
          name: "效率机器",
          type: "productive",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1594381898411-846e7d193883?w=400&h=300&fit=crop",
          stats: { energy: 95, discipline: 90, efficiency: 85 },
          quote: "我已经优化了我的周末以达到最大产出！",
          rarity: "rare",
        },
        {
          id: "productive_2_evolved",
          name: "知识巨擘",
          type: "productive",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1513475382585-d06e58bcb0e0?w=400&h=300&fit=crop",
          stats: { knowledge: 95, memory: 90, comprehension: 88 },
          quote: "知识就是力量，我已经无所不知！",
          rarity: "rare",
        },
        {
          id: "productive_3_evolved",
          name: "商业奇才",
          type: "productive",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1507679799987-c73779587ccf?w=400&h=300&fit=crop",
          stats: { business: 92, innovation: 88, planning: 90 },
          quote: "我的下一个项目将改变世界！",
          rarity: "rare",
        },
        {
          id: "productive_4_evolved",
          name: "艺术大师",
          type: "productive",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1501084817091-a4f3d1d19e07?w=400&h=300&fit=crop",
          stats: { creativity: 95, expression: 92, skill: 88 },
          quote: "艺术就是我的生命，我的作品就是我的语言！",
          rarity: "rare",
        },
        {
          id: "productive_5_evolved",
          name: "体育冠军",
          type: "productive",
          level: 2,
          image:
            "https://images.unsplash.com/photo-1526676338756-d708c65a0f08?w=400&h=300&fit=crop",
          stats: { strength: 92, agility: 88, endurance: 90 },
          quote: "突破极限，超越自我，永不止步！",
          rarity: "rare",
        },

        // 特殊人格
        {
          id: "special_fence_sitter",
          name: "骑墙派",
          type: "special",
          level: 1,
          image:
            "https://images.unsplash.com/photo-1516450360452-9312f5e86fc7?w=400&h=300&fit=crop",
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
            "https://images.unsplash.com/photo-1501139083538-0139583c060f?w=400&h=300&fit=crop",
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
        // 获取所有基础级别的人格
        const lazyOptions = this.personalities.filter(
          (p) => p.type === "lazy" && p.level === 1
        );
        const productiveOptions = this.personalities.filter(
          (p) => p.type === "productive" && p.level === 1
        );

        // 随机选择一个摆烂方和一个卷王方的人格
        const randomLazy =
          lazyOptions[Math.floor(Math.random() * lazyOptions.length)];
        const randomProductive =
          productiveOptions[
            Math.floor(Math.random() * productiveOptions.length)
          ];

        this.currentBattle = {
          lazy: { ...randomLazy },
          productive: { ...randomProductive },
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
  max-width: 800px;
  width: 100%;
  margin: 0 auto;
  padding: 2rem;
  position: relative;
}

.header-section {
  text-align: center;
  margin-bottom: 2rem;
}

.header-title {
  font-size: 2rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
  color: var(--text-primary);
}

.header-subtitle {
  font-size: 1rem;
  color: var(--text-secondary);
  max-width: 600px;
  margin: 0 auto;
}

.input-section {
  background: var(--bg-card);
  padding: 1.5rem;
  border-radius: var(--border-radius-md);
  margin-bottom: 2rem;
  border: 1px solid var(--secondary);
}

.input-group {
  display: flex;
  gap: 1rem;
}

.input-field {
  flex: 1;
  padding: 0.75rem;
  background: var(--bg-light);
  border: 1px solid var(--secondary);
  border-radius: var(--border-radius-sm);
  color: var(--text-primary);
}

.generate-btn {
  background: var(--primary);
  color: var(--text-primary);
  padding: 0.75rem 1.5rem;
  border-radius: var(--border-radius-sm);
  border: 1px solid var(--secondary);
  font-weight: 500;
}

.generate-btn:hover {
  background: var(--bg-light);
  border-color: var(--accent);
}

.collection-display {
  background: var(--bg-card);
  padding: 1.5rem;
  border-radius: var(--border-radius-md);
  border: 1px solid var(--secondary);
}

.collection-title {
  font-size: 1.5rem;
  color: var(--text-primary);
  margin-bottom: 1.5rem;
  font-weight: 600;
  text-align: center;
}

.collection-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
}

@media (max-width: 768px) {
  .game-container {
    padding: 1rem;
  }

  .input-group {
    flex-direction: column;
  }

  .generate-btn {
    width: 100%;
  }
}
</style>
