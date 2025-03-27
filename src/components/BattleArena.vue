<template>
  <section class="battle-arena">
    <h2 class="battle-title">人格对决！</h2>
    <p class="battle-subtitle">
      {{ currentPhase === "selection" ? "选择支持的一方" : "对战进行中..." }}
    </p>

    <div class="battle-container">
      <div
        class="personality-card lazy-side"
        :class="{
          selected: selectedSide === 'lazy',
          attacking: currentPhase === 'battle' && currentAttacker === 'lazy',
          defending:
            currentPhase === 'battle' && currentAttacker === 'productive',
        }"
        @click="handleCardClick('lazy')"
      >
        <div class="hp-bar">
          <div class="hp-fill" :style="{ width: `${lazyHP}%` }"></div>
          <span class="hp-text">HP: {{ lazyHP }}/100</span>
        </div>

        <div class="card-animation">
          <div class="image-container">
            <img
              :src="lazyPersonality.image"
              alt="摆烂人格"
              class="character-img"
            />
            <div class="image-decoration left"></div>
            <div class="image-decoration right"></div>
          </div>
        </div>

        <div class="card-content">
          <h3 class="character-name">{{ lazyPersonality.name }}</h3>
          <div class="character-quote">"{{ currentLazyDialog }}"</div>
        </div>

        <div class="battle-stats" v-if="currentPhase === 'battle'">
          <div
            class="stat-item"
            v-for="(value, stat) in lazyPersonality.stats"
            :key="stat"
          >
            <div class="stat-name">{{ formatStatName(stat) }}</div>
            <div class="stat-value">{{ value }}</div>
          </div>
        </div>
      </div>

      <div class="vs-container">
        <div
          class="vs-badge"
          :class="{ 'battle-active': currentPhase === 'battle' }"
        >
          <template v-if="currentPhase === 'selection'">VS</template>
          <template v-else>
            <span class="round-info">第{{ currentRound }}回合</span>
          </template>
        </div>
      </div>

      <div
        class="personality-card productive-side"
        :class="{
          selected: selectedSide === 'productive',
          attacking:
            currentPhase === 'battle' && currentAttacker === 'productive',
          defending: currentPhase === 'battle' && currentAttacker === 'lazy',
        }"
        @click="handleCardClick('productive')"
      >
        <div class="hp-bar">
          <div class="hp-fill" :style="{ width: `${productiveHP}%` }"></div>
          <span class="hp-text">HP: {{ productiveHP }}/100</span>
        </div>

        <div class="card-animation">
          <div class="image-container">
            <img
              :src="productivePersonality.image"
              alt="卷王人格"
              class="character-img"
            />
            <div class="image-decoration left"></div>
            <div class="image-decoration right"></div>
          </div>
        </div>

        <div class="card-content">
          <h3 class="character-name">{{ productivePersonality.name }}</h3>
          <div class="character-quote">"{{ currentProductiveDialog }}"</div>
        </div>

        <div class="battle-stats" v-if="currentPhase === 'battle'">
          <div
            class="stat-item"
            v-for="(value, stat) in productivePersonality.stats"
            :key="stat"
          >
            <div class="stat-name">{{ formatStatName(stat) }}</div>
            <div class="stat-value">{{ value }}</div>
          </div>
        </div>
      </div>
    </div>

    <div class="battle-actions" v-if="currentPhase === 'battle'">
      <div class="action-message" :class="{ 'fade-in': showActionMessage }">
        {{ actionMessage }}
      </div>
      <div class="damage-number" :class="{ show: showDamage }">
        -{{ currentDamage }}
      </div>
    </div>

    <div class="battle-result" v-if="battleEnded">
      <div class="result-content">
        <h3 class="result-title">
          {{ winner === selectedSide ? "胜利！" : "失败！" }}
        </h3>
        <p class="result-message">
          {{ getResultMessage() }}
        </p>
        <button class="confirm-button" @click="confirmResult">确认</button>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: "BattleArena",
  props: {
    lazyPersonality: {
      type: Object,
      required: true,
    },
    productivePersonality: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      currentPhase: "selection", // 'selection' | 'battle' | 'result'
      selectedSide: null,
      currentRound: 1,
      maxRounds: 3,
      lazyHP: 100,
      productiveHP: 100,
      currentAttacker: null,
      currentDefender: null,
      battleEnded: false,
      winner: null,
      showActionMessage: false,
      showDamage: false,
      currentDamage: 0,
      actionMessage: "",
      currentLazyDialog: "",
      currentProductiveDialog: "",
      lazyDialogs: {
        attack: ["摆烂也是一种力量！", "躺平即是正义！", "让我用懒散打败你！"],
        defend: ["我要躺平防御！", "这么累干嘛...", "不要打扰我睡觉！"],
        win: ["摆烂才是人生真谛！", "躺赢是最好的赢法！"],
        lose: ["输了也无所谓，继续躺...", "好累啊，不玩了..."],
      },
      productiveDialogs: {
        attack: [
          "加油加油不要停！",
          "效率就是生产力！",
          "让我看看你的进取心！",
        ],
        defend: ["防御也要高效率！", "这点打击算什么！", "我还能更努力！"],
        win: ["努力就会有回报！", "胜利属于奋斗者！"],
        lose: ["失败是成功之母！", "我要加倍努力！"],
      },
    };
  },
  mounted() {
    // 开始对话对决
    this.startDialogBattle();

    // 设置滑动检测
    const battleContainer = this.$el.querySelector(".battle-container");
    battleContainer.addEventListener("touchstart", this.handleTouchStart);
    battleContainer.addEventListener("touchend", this.handleTouchEnd);
  },
  beforeUnmount() {
    // 清理事件监听器
    const battleContainer = this.$el.querySelector(".battle-container");
    battleContainer.removeEventListener("touchstart", this.handleTouchStart);
    battleContainer.removeEventListener("touchend", this.handleTouchEnd);
  },
  methods: {
    formatStatName(stat) {
      const statMap = {
        laziness: "摆烂指数",
        comfort: "舒适度",
        sleepQuality: "睡眠质量",
        energy: "能量值",
        discipline: "自律值",
        efficiency: "效率值",
      };
      return statMap[stat] || stat;
    },
    startDialogBattle() {
      // 开始对话对决
      this.dialogInterval = setInterval(() => {
        this.dialogStep = (this.dialogStep + 1) % 3;
      }, 3000);

      // 如果没有选择，一段时间后停止对话
      setTimeout(() => {
        if (!this.selectedSide) {
          clearInterval(this.dialogInterval);
          this.showDialog = false;
        }
      }, 9000);
    },
    selectPersonality(side) {
      this.selectedSide = side;
      clearInterval(this.dialogInterval);
      this.showDialog = false;

      // 动画后发出选择事件
      setTimeout(() => {
        this.$emit("select-personality", side);
      }, 500);
    },
    handleTouchStart(e) {
      this.touchStartX = e.changedTouches[0].screenX;
    },
    handleTouchEnd(e) {
      this.touchEndX = e.changedTouches[0].screenX;
      this.handleSwipe();
    },
    handleSwipe() {
      const SWIPE_THRESHOLD = 50;
      if (this.touchEndX < this.touchStartX - SWIPE_THRESHOLD) {
        // 向左滑动（懒惰）
        this.selectPersonality("lazy");
      }
      if (this.touchEndX > this.touchStartX + SWIPE_THRESHOLD) {
        // 向右滑动（高效）
        this.selectPersonality("productive");
      }
    },
    handleCardClick(side) {
      if (this.currentPhase === "selection" && !this.selectedSide) {
        this.selectedSide = side;
        this.startBattle();
      }
    },
    async startBattle() {
      this.currentPhase = "battle";
      await this.wait(1000);
      this.runBattleRound();
    },
    async runBattleRound() {
      if (this.currentRound <= this.maxRounds && !this.battleEnded) {
        // 决定谁先攻击
        this.currentAttacker = Math.random() < 0.5 ? "lazy" : "productive";
        this.currentDefender =
          this.currentAttacker === "lazy" ? "productive" : "lazy";

        // 第一次攻击
        await this.performAttack();
        if (!this.checkBattleEnd()) {
          // 交换攻守
          [this.currentAttacker, this.currentDefender] = [
            this.currentDefender,
            this.currentAttacker,
          ];
          // 第二次攻击
          await this.performAttack();
          this.checkBattleEnd();
        }

        if (!this.battleEnded) {
          this.currentRound++;
          if (this.currentRound <= this.maxRounds) {
            this.runBattleRound();
          } else {
            this.endBattle();
          }
        }
      }
    },
    async performAttack() {
      // 更新对话
      if (this.currentAttacker === "lazy") {
        this.currentLazyDialog = this.getRandomDialog(this.lazyDialogs.attack);
        this.currentProductiveDialog = this.getRandomDialog(
          this.productiveDialogs.defend
        );
      } else {
        this.currentProductiveDialog = this.getRandomDialog(
          this.productiveDialogs.attack
        );
        this.currentLazyDialog = this.getRandomDialog(this.lazyDialogs.defend);
      }

      // 计算伤害
      const attackerStats =
        this.currentAttacker === "lazy"
          ? this.lazyPersonality.stats
          : this.productivePersonality.stats;

      const damage = this.calculateDamage(attackerStats);
      this.currentDamage = damage;

      // 显示动作信息
      this.actionMessage =
        this.currentAttacker === "lazy" ? "摆烂流·奥义！" : "卷王决·终极！";
      this.showActionMessage = true;
      await this.wait(1000);
      this.showActionMessage = false;

      // 显示伤害
      this.showDamage = true;
      await this.wait(800);
      this.showDamage = false;

      // 扣血
      if (this.currentAttacker === "lazy") {
        this.productiveHP = Math.max(0, this.productiveHP - damage);
      } else {
        this.lazyHP = Math.max(0, this.lazyHP - damage);
      }

      await this.wait(500);
    },
    calculateDamage(stats) {
      // 根据属性计算伤害
      const baseValue =
        Object.values(stats).reduce((a, b) => a + b, 0) /
        Object.values(stats).length;
      const damage = Math.floor(baseValue * (Math.random() * 0.4 + 0.8)); // 伤害浮动 80%-120%
      return Math.min(40, Math.max(15, damage)); // 伤害范围 15-40
    },
    checkBattleEnd() {
      if (this.lazyHP <= 0 || this.productiveHP <= 0) {
        this.endBattle();
        return true;
      }
      return false;
    },
    endBattle() {
      // 决定胜者
      if (this.lazyHP === this.productiveHP) {
        this.winner = Math.random() < 0.5 ? "lazy" : "productive";
      } else {
        this.winner = this.lazyHP > this.productiveHP ? "lazy" : "productive";
      }

      // 更新最终对话
      this.currentLazyDialog = this.getRandomDialog(
        this.winner === "lazy" ? this.lazyDialogs.win : this.lazyDialogs.lose
      );
      this.currentProductiveDialog = this.getRandomDialog(
        this.winner === "productive"
          ? this.productiveDialogs.win
          : this.productiveDialogs.lose
      );

      this.battleEnded = true;
    },
    getResultMessage() {
      if (this.winner === this.selectedSide) {
        return this.selectedSide === "lazy"
          ? "原来躺平才是最强的！"
          : "奋斗才是正确的选择！";
      }
      return this.selectedSide === "lazy"
        ? "看来还是要适当奋斗啊..."
        : "有时候也要学会放松呢...";
    },
    confirmResult() {
      this.$emit("select-personality", this.selectedSide);
    },
    getRandomDialog(dialogs) {
      return dialogs[Math.floor(Math.random() * dialogs.length)];
    },
    wait(ms) {
      return new Promise((resolve) => setTimeout(resolve, ms));
    },
  },
};
</script>

<style scoped>
.battle-arena {
  background: var(--bg-card);
  border-radius: var(--border-radius-lg);
  padding: 2rem;
  margin: 2rem 0;
  box-shadow: var(--shadow-md);
  position: relative;
  overflow: hidden;
  border: 1px solid rgba(67, 97, 238, 0.2);
}

.battle-arena::before,
.battle-arena::after {
  content: "";
  position: absolute;
  width: 300px;
  height: 300px;
  border-radius: 50%;
  filter: blur(40px);
  opacity: 0.15;
  z-index: 0;
}

.battle-arena::before {
  top: -100px;
  right: -100px;
  background: var(--primary);
}

.battle-arena::after {
  bottom: -100px;
  left: -100px;
  background: var(--secondary);
}

.battle-title {
  text-align: center;
  font-size: 3rem;
  margin-bottom: 0.5rem;
  color: var(--text-primary);
  position: relative;
  width: 100%;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 2px;
  background: var(--primary-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-shadow: 3px 3px 6px rgba(0, 0, 0, 0.2);
}

.battle-subtitle {
  text-align: center;
  font-size: 1.2rem;
  color: var(--text-secondary);
  margin-bottom: 3rem;
  width: 100%;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.battle-container {
  display: flex;
  align-items: stretch;
  justify-content: space-between;
  gap: 2rem;
  margin-bottom: 2rem;
  position: relative;
  z-index: 1;
}

.personality-card {
  flex: 1;
  background: var(--bg-light);
  border-radius: var(--border-radius-lg);
  padding: 1.5rem;
  text-align: center;
  transition: var(--transition-bounce);
  box-shadow: var(--shadow-md);
  position: relative;
  overflow: hidden;
  z-index: 1;
  border: 1px solid rgba(67, 97, 238, 0.2);
}

.personality-card.lazy-side {
  background: linear-gradient(
    135deg,
    var(--bg-light),
    var(--bg-light) 60%,
    rgba(67, 97, 238, 0.1)
  );
}

.personality-card.productive-side {
  background: linear-gradient(
    135deg,
    var(--bg-light),
    var(--bg-light) 60%,
    rgba(0, 245, 212, 0.1)
  );
}

.personality-card::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 4px;
  z-index: 2;
}

.lazy-side::before {
  background: var(--primary-gradient);
}

.productive-side::before {
  background: var(--secondary-gradient);
}

.card-animation {
  height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: var(--border-radius-lg);
  margin-bottom: 1.5rem;
  overflow: hidden;
  position: relative;
  background: var(--bg-dark);
}

.image-container {
  position: relative;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.character-img {
  width: 180px;
  height: 180px;
  object-fit: cover;
  border-radius: 50%;
  transition: var(--transition);
  z-index: 1;
  border: 3px solid transparent;
}

.lazy-side .character-img {
  border-color: var(--primary);
  box-shadow: 0 0 20px rgba(67, 97, 238, 0.3);
}

.productive-side .character-img {
  border-color: var(--secondary);
  box-shadow: 0 0 20px rgba(0, 245, 212, 0.3);
}

.image-decoration {
  position: absolute;
  z-index: 0;
  opacity: 0.3;
  filter: blur(2px);
  transition: var(--transition);
}

.image-decoration.left {
  width: 60px;
  height: 60px;
  left: 15px;
  top: 15px;
  border-radius: var(--border-radius-sm);
  background: var(--primary);
  transform: rotate(-15deg);
}

.image-decoration.right {
  width: 40px;
  height: 40px;
  right: 15px;
  bottom: 15px;
  border-radius: var(--border-radius-sm);
  background: var(--primary);
  transform: rotate(15deg);
}

.productive-side .image-decoration.left,
.productive-side .image-decoration.right {
  background: var(--secondary);
}

.vs-container {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  z-index: 10;
}

.vs-badge {
  background: var(--bg-dark);
  color: var(--text-primary);
  font-size: 1.8rem;
  font-weight: 900;
  width: 70px;
  height: 70px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: var(--shadow-lg), 0 0 20px rgba(67, 97, 238, 0.3);
  transition: var(--transition-bounce);
  text-transform: uppercase;
  letter-spacing: 1px;
  border: 2px solid var(--primary);
}

.character-name {
  font-size: 1.8rem;
  margin-bottom: 0.8rem;
  color: var(--text-primary);
  font-weight: 800;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.lazy-side .character-name {
  background: var(--primary-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.productive-side .character-name {
  background: var(--secondary-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.character-quote {
  font-size: 1.1rem;
  color: var(--text-secondary);
  font-style: italic;
  padding: 1rem 1.5rem;
  border-radius: var(--border-radius-md);
  position: relative;
  line-height: 1.4;
  font-weight: 500;
  background: var(--bg-dark);
}

.lazy-side .character-quote {
  box-shadow: inset 0 0 20px rgba(67, 97, 238, 0.1);
}

.productive-side .character-quote {
  box-shadow: inset 0 0 20px rgba(0, 245, 212, 0.1);
}

.character-quote::before {
  content: '"';
  position: absolute;
  top: -5px;
  left: 10px;
  font-size: 2.5rem;
  font-family: serif;
  opacity: 0.2;
}

.lazy-side .character-quote::before {
  color: var(--primary);
}

.productive-side .character-quote::before {
  color: var(--secondary);
}

.battle-dialog {
  min-height: 80px;
  position: relative;
  margin-bottom: 1.5rem;
}

.dialog-bubble {
  position: absolute;
  padding: 1rem 1.5rem;
  border-radius: var(--border-radius-lg);
  max-width: 80%;
  animation: bubble-in 0.5s ease forwards;
  box-shadow: var(--shadow-md);
  font-weight: 500;
  color: var(--text-primary);
}

@keyframes bubble-in {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.lazy-bubble {
  background: var(--primary-gradient);
  left: 10%;
}

.lazy-bubble:after {
  content: "";
  position: absolute;
  left: 20px;
  top: -12px;
  border-width: 0 12px 12px;
  border-style: solid;
  border-color: var(--primary) transparent;
}

.productive-bubble {
  background: var(--secondary-gradient);
  right: 10%;
}

.productive-bubble:after {
  content: "";
  position: absolute;
  right: 20px;
  top: -12px;
  border-width: 0 12px 12px;
  border-style: solid;
  border-color: var(--secondary) transparent;
}

.swipe-instructions {
  display: flex;
  justify-content: space-between;
  color: var(--text-secondary);
  font-size: 1rem;
  padding: 0 2rem;
  animation: pulse 2s infinite;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 1px;
}

/* 悬停效果 */
.personality-card:hover {
  transform: translateY(-15px) scale(1.02);
  box-shadow: var(--shadow-lg), 0 0 30px rgba(67, 97, 238, 0.2);
}

.personality-card:hover .character-img {
  transform: scale(1.05);
  box-shadow: 0 0 30px rgba(67, 97, 238, 0.4);
}

.personality-card:hover .image-decoration.left {
  transform: scale(1.1) rotate(-25deg) translate(-5px, -5px);
  opacity: 0.4;
}

.personality-card:hover .image-decoration.right {
  transform: scale(1.1) rotate(25deg) translate(5px, 5px);
  opacity: 0.4;
}

.vs-badge:hover {
  transform: scale(1.1) rotate(360deg);
  box-shadow: var(--shadow-lg), 0 0 30px rgba(67, 97, 238, 0.4);
}

.selected {
  transform: translateY(-15px) scale(1.02);
  box-shadow: var(--shadow-lg);
  z-index: 5;
}

.selected.lazy-side {
  box-shadow: var(--shadow-lg), 0 0 40px rgba(67, 97, 238, 0.3);
}

.selected.productive-side {
  box-shadow: var(--shadow-lg), 0 0 40px rgba(0, 245, 212, 0.3);
}

.selected .character-img {
  transform: scale(1.05);
}

@keyframes pulse {
  0% {
    opacity: 0.6;
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0.6;
  }
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

@media (max-width: 768px) {
  .battle-arena {
    padding: 1.5rem;
    margin: 1rem 0;
  }

  .battle-title {
    font-size: 2.2rem;
  }

  .battle-subtitle {
    font-size: 1rem;
    margin-bottom: 1.5rem;
  }

  .battle-container {
    flex-direction: column;
    gap: 2rem;
  }

  .vs-container {
    position: relative;
    left: auto;
    top: auto;
    transform: none;
    margin: -1rem auto 1rem;
    z-index: 10;
  }

  .vs-badge {
    width: 60px;
    height: 60px;
    font-size: 1.5rem;
  }

  .character-img {
    width: 150px;
    height: 150px;
  }

  .character-name {
    font-size: 1.6rem;
  }

  .character-quote {
    font-size: 1rem;
    padding: 1rem;
  }

  .image-decoration.left {
    width: 40px;
    height: 40px;
  }

  .image-decoration.right {
    width: 30px;
    height: 30px;
  }
}

/* 添加新的战斗相关样式 */
.hp-bar {
  position: absolute;
  top: 0.5rem;
  left: 0.5rem;
  right: 0.5rem;
  height: 8px;
  background: var(--bg-dark);
  border-radius: 4px;
  overflow: hidden;
  z-index: 2;
}

.hp-fill {
  height: 100%;
  background: linear-gradient(90deg, #2ecc71, #27ae60);
  transition: width 0.5s ease;
}

.hp-text {
  position: absolute;
  top: -1.2rem;
  right: 0;
  font-size: 0.8rem;
  color: var(--text-secondary);
}

.battle-stats {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.5rem;
  padding: 1rem;
  background: var(--bg-dark);
  border-radius: var(--border-radius-md);
  margin-top: 1rem;
}

.stat-item {
  text-align: center;
}

.stat-name {
  font-size: 0.8rem;
  color: var(--text-secondary);
  margin-bottom: 0.2rem;
}

.stat-value {
  font-size: 1.1rem;
  font-weight: 600;
  color: var(--text-primary);
}

.personality-card.attacking {
  transform: translateX(20px) scale(1.05);
  z-index: 2;
}

.personality-card.defending {
  transform: translateX(-20px);
  opacity: 0.8;
}

.battle-actions {
  position: relative;
  height: 60px;
  margin-top: 1rem;
  text-align: center;
}

.action-message {
  font-size: 2rem;
  font-weight: 900;
  color: var(--text-primary);
  opacity: 0;
  transform: translateY(20px);
  transition: all 0.3s ease;
}

.action-message.fade-in {
  opacity: 1;
  transform: translateY(0);
}

.damage-number {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%) scale(0);
  font-size: 3rem;
  font-weight: 900;
  color: #e74c3c;
  opacity: 0;
  transition: all 0.3s ease;
}

.damage-number.show {
  transform: translate(-50%, -50%) scale(1);
  opacity: 1;
}

.vs-badge.battle-active {
  animation: pulse 2s infinite;
}

.round-info {
  font-size: 1rem;
  font-weight: 600;
}

.battle-result {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 100;
  animation: fadeIn 0.5s ease;
}

.result-content {
  background: var(--bg-card);
  padding: 2rem;
  border-radius: var(--border-radius-lg);
  text-align: center;
  animation: slideUp 0.5s ease;
}

.result-title {
  font-size: 3rem;
  font-weight: 900;
  margin-bottom: 1rem;
  background: var(--primary-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.result-message {
  font-size: 1.2rem;
  color: var(--text-secondary);
  margin-bottom: 2rem;
}

.confirm-button {
  background: var(--primary-gradient);
  color: var(--text-primary);
  padding: 1rem 2rem;
  border-radius: var(--border-radius-lg);
  font-size: 1.1rem;
  font-weight: 600;
  transition: var(--transition);
}

.confirm-button:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 768px) {
  .battle-container {
    flex-direction: column;
    gap: 3rem;
  }

  .vs-container {
    position: relative;
    transform: none;
    margin: 1rem 0;
  }

  .personality-card.attacking {
    transform: translateY(10px) scale(1.05);
  }

  .personality-card.defending {
    transform: translateY(-10px);
  }

  .battle-stats {
    grid-template-columns: repeat(3, 1fr);
  }

  .result-title {
    font-size: 2rem;
  }

  .result-message {
    font-size: 1rem;
  }
}
</style>
