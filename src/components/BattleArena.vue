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

    <div class="battle-result" v-if="currentPhase === 'result'">
      <div class="result-content">
        <h2 class="result-title">
          {{
            (selectedSide === "lazy" && lazyHP > productiveHP) ||
            (selectedSide === "productive" && productiveHP > lazyHP)
              ? "胜利！"
              : "失败！"
          }}
        </h2>
        <p class="result-message">
          {{ getResultMessage() }}
        </p>

        <!-- MBTI分析 -->
        <div class="mbti-analysis">
          <div class="mbti-title">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
            >
              <path
                d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"
              ></path>
              <polyline points="3.27 6.96 12 12.01 20.73 6.96"></polyline>
              <line x1="12" y1="22.08" x2="12" y2="12"></line>
            </svg>
            MBTI 性格分析
          </div>
          <div class="mbti-content">
            {{ getMBTIAnalysis() }}
          </div>
        </div>

        <!-- 今日运势 -->
        <div class="fortune-analysis">
          <div class="fortune-title">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
            >
              <circle cx="12" cy="12" r="10"></circle>
              <path d="M12 6v6l4 2"></path>
            </svg>
            今日运势
          </div>
          <div class="fortune-content">
            {{ getDailyFortune() }}
          </div>
          <div class="fortune-tags">
            <span class="fortune-tag" v-for="tag in fortuneTags" :key="tag">{{
              tag
            }}</span>
          </div>
        </div>

        <button @click="restartBattle" class="confirm-button">再来一次</button>

        <!-- 游戏推荐列表 -->
        <div class="game-recommends result-game-recommends">
          <h3 class="recommend-title">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
            >
              <path
                d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5L17 3z"
              ></path>
            </svg>
            你该试试这些游戏！
          </h3>

          <div class="game-list">
            <div
              v-for="(game, index) in gameList"
              :key="index"
              class="game-card"
            >
              <div class="game-icon">
                <img :src="game.icon" :alt="game.name" />
              </div>
              <div class="game-info">
                <h4 class="game-name">{{ game.name }}</h4>
                <p class="game-desc">{{ game.description }}</p>
                <div class="game-tags">
                  <span
                    v-for="(tag, i) in game.tags"
                    :key="i"
                    class="game-tag"
                    >{{ tag }}</span
                  >
                </div>
              </div>
              <a :href="game.link" class="install-btn" target="_blank">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                >
                  <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
                  <polyline points="7 10 12 15 17 10"></polyline>
                  <line x1="12" y1="15" x2="12" y2="3"></line>
                </svg>
                安装
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="battle-controls" v-if="currentPhase === 'battle'">
      <button class="skip-button" @click="skipBattle">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <polygon points="5 4 15 12 5 20 5 4"></polygon>
          <line x1="19" y1="5" x2="19" y2="19"></line>
        </svg>
        跳过对战
      </button>
    </div>

    <!-- 游戏推荐列表 -->
    <div class="game-recommends">
      <h3 class="recommend-title">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <path
            d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5L17 3z"
          ></path>
        </svg>
        你该试试这些游戏！
      </h3>

      <div class="game-list">
        <div v-for="(game, index) in gameList" :key="index" class="game-card">
          <div class="game-icon">
            <img :src="game.icon" :alt="game.name" />
          </div>
          <div class="game-info">
            <h4 class="game-name">{{ game.name }}</h4>
            <p class="game-desc">{{ game.description }}</p>
            <div class="game-tags">
              <span v-for="(tag, i) in game.tags" :key="i" class="game-tag">{{
                tag
              }}</span>
            </div>
          </div>
          <a :href="game.link" class="install-btn" target="_blank">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
            >
              <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
              <polyline points="7 10 12 15 17 10"></polyline>
              <line x1="12" y1="15" x2="12" y2="3"></line>
            </svg>
            安装
          </a>
        </div>
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
      showResult: false,
      fortuneTags: [],
      mbtiType: "", // 存储生成的MBTI类型
      fortuneMessage: "", // 存储生成的运势消息
      gameList: [
        {
          name: "摸鱼达人2023",
          icon: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' rx='20' fill='%234361ee' opacity='0.1'/%3E%3Ccircle cx='50' cy='50' r='40' fill='%234361ee' opacity='0.2'/%3E%3Cpath d='M65 35H35c-2.8 0-5 2.2-5 5v20c0 2.8 2.2 5 5 5h30c2.8 0 5-2.2 5-5V40c0-2.8-2.2-5-5-5z' fill='%234361ee'/%3E%3Cpath d='M45 45h10v10H45z' fill='white'/%3E%3Cpath d='M60 45h5v5h-5zM60 55h5v5h-5zM35 45h5v5h-5zM35 55h5v5h-5z' fill='white'/%3E%3Cpath d='M40 70c0 1.1 0.9 2 2 2h16c1.1 0 2-0.9 2-2v-5H40v5z' fill='%234361ee'/%3E%3Cpath d='M50 30c-1.1 0-2 0.9-2 2v3h4v-3c0-1.1-0.9-2-2-2z' fill='%234361ee'/%3E%3C/svg%3E",
          description:
            "挑战你懒惰的极限，看看你能在不被炒鱿鱼的情况下摸鱼多久。",
          tags: ["策略", "休闲", "模拟"],
          link: "https://store.steampowered.com",
        },
        {
          name: "效率冲击：终极任务",
          icon: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' rx='20' fill='%2300f5d4' opacity='0.1'/%3E%3Ccircle cx='50' cy='50' r='40' fill='%2300f5d4' opacity='0.2'/%3E%3Cpath d='M75 35H65V25c0-1.1-0.9-2-2-2H37c-1.1 0-2 0.9-2 2v10H25c-1.1 0-2 0.9-2 2v38c0 1.1 0.9 2 2 2h50c1.1 0 2-0.9 2-2V37c0-1.1-0.9-2-2-2z' fill='%2300f5d4'/%3E%3Cpath d='M39 27h22v8H39z' fill='white'/%3E%3Cpath d='M40 50l5-5 5 5 10-10 5 5' fill='none' stroke='white' stroke-width='2'/%3E%3Ccircle cx='30' cy='65' r='3' fill='white'/%3E%3Ccircle cx='40' cy='65' r='3' fill='white'/%3E%3Ccircle cx='50' cy='65' r='3' fill='white'/%3E%3Ccircle cx='60' cy='65' r='3' fill='white'/%3E%3Ccircle cx='70' cy='65' r='3' fill='white'/%3E%3C/svg%3E",
          description: "通过时间管理挑战和任务规划，成为真正的效率专家。",
          tags: ["管理", "自我提升", "模拟"],
          link: "https://apps.apple.com",
        },
        {
          name: "性格迷城：MBTI探险",
          icon: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' rx='20' fill='%23f687b3' opacity='0.1'/%3E%3Ccircle cx='50' cy='50' r='40' fill='%23f687b3' opacity='0.2'/%3E%3Cpath d='M30 30h40v40H30z' fill='%23f687b3'/%3E%3Ccircle cx='40' cy='40' r='5' fill='white'/%3E%3Ccircle cx='60' cy='40' r='5' fill='white'/%3E%3Cpath d='M35 55c0 8.3 6.7 15 15 15s15-6.7 15-15H35z' fill='white'/%3E%3Cpath d='M25 75l10-10M75 75l-10-10' stroke='%23f687b3' stroke-width='3'/%3E%3Ccircle cx='20' cy='80' r='5' fill='%23f687b3'/%3E%3Ccircle cx='80' cy='80' r='5' fill='%23f687b3'/%3E%3Cpath d='M40 25c0-5.5-4.5-10-10-10v10h10zM60 25c0-5.5 4.5-10 10-10v10H60z' fill='%23f687b3'/%3E%3C/svg%3E",
          description: "探索16种性格类型的奇妙世界，结交志同道合的朋友。",
          tags: ["社交", "角色扮演", "探索"],
          link: "https://play.google.com",
        },
        {
          name: "平衡大师：天秤传说",
          icon: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' rx='20' fill='%235f27cd' opacity='0.1'/%3E%3Ccircle cx='50' cy='50' r='40' fill='%235f27cd' opacity='0.2'/%3E%3Cpath d='M50 20v60' stroke='%235f27cd' stroke-width='4'/%3E%3Cpath d='M30 70h40' stroke='%235f27cd' stroke-width='4'/%3E%3Ccircle cx='50' cy='75' r='5' fill='%235f27cd'/%3E%3Cpath d='M25 45h50' stroke='%235f27cd' stroke-width='2'/%3E%3Ccircle cx='35' cy='45' r='10' fill='%235f27cd'/%3E%3Ccircle cx='65' cy='45' r='5' fill='%235f27cd'/%3E%3Cpath d='M20 30c0-5.5 4.5-10 10-10s10 4.5 10 10h-20zM60 30c0-5.5 4.5-10 10-10s10 4.5 10 10H60z' fill='%235f27cd'/%3E%3C/svg%3E",
          description: "在工作和生活之间找到完美平衡，成为平衡艺术大师。",
          tags: ["休闲", "益智", "生活模拟"],
          link: "https://store.steampowered.com",
        },
      ],
    };
  },
  mounted() {
    // 初始化对话
    this.currentLazyDialog = this.getDialogMessage("lazy", "normal");
    this.currentProductiveDialog = this.getDialogMessage(
      "productive",
      "normal"
    );

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
      // 定时更新对话
      this.dialogInterval = setInterval(() => {
        this.currentLazyDialog = this.getDialogMessage("lazy", "normal");
        this.currentProductiveDialog = this.getDialogMessage(
          "productive",
          "normal"
        );
      }, 3000);

      // 如果没有选择，一段时间后停止对话
      setTimeout(() => {
        if (!this.selectedSide) {
          clearInterval(this.dialogInterval);
        }
      }, 9000);
    },
    selectPersonality(side) {
      this.selectedSide = side;
      clearInterval(this.dialogInterval);

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
      // 初始化战斗
      this.currentPhase = "battle";
      this.lazyHP = 100;
      this.productiveHP = 100;
      this.currentRound = 1;

      // 随机决定先手
      this.currentAttacker = Math.random() < 0.5 ? "lazy" : "productive";
      this.currentDefender =
        this.currentAttacker === "lazy" ? "productive" : "lazy";

      // 延迟一小段时间后开始战斗
      await this.wait(1000);
      this.runBattleRound();
    },
    async runBattleRound() {
      // 攻击
      this.actionMessage =
        this.currentAttacker === "lazy" ? "摆烂攻击！" : "卷王进攻！";
      this.showActionMessage = true;
      await this.wait(800);
      this.showActionMessage = false;

      await this.performAttack();

      // 检查战斗是否结束
      if (this.lazyHP <= 0 || this.productiveHP <= 0 || this.currentRound > 3) {
        this.endBattle();
        return;
      }

      // 切换攻击者
      this.currentAttacker =
        this.currentAttacker === "lazy" ? "productive" : "lazy";
      this.currentDefender =
        this.currentDefender === "lazy" ? "productive" : "lazy";
      this.currentRound++;

      // 继续下一轮
      await this.wait(1000);
      this.runBattleRound();
    },
    async performAttack() {
      // 更新对话
      if (this.currentAttacker === "lazy") {
        this.currentLazyDialog = this.getDialogMessage("lazy", "attack");
        this.currentProductiveDialog = this.getDialogMessage(
          "productive",
          "defend"
        );
      } else {
        this.currentProductiveDialog = this.getDialogMessage(
          "productive",
          "attack"
        );
        this.currentLazyDialog = this.getDialogMessage("lazy", "defend");
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
      this.currentPhase = "result";

      // 生成并保存MBTI类型和运势
      this.mbtiType = this.generateMBTIType();
      this.fortuneMessage = this.generateFortuneMessage();
      this.generateFortuneTags();

      // 判断胜负
      if (this.lazyHP <= 0) {
        this.winner = "productive";
      } else if (this.productiveHP <= 0) {
        this.winner = "lazy";
      } else if (this.currentRound > 3) {
        // 三回合后比较血量
        this.winner = this.lazyHP > this.productiveHP ? "lazy" : "productive";
      }
    },
    getResultMessage() {
      const isWinner =
        (this.selectedSide === "lazy" && this.lazyHP > this.productiveHP) ||
        (this.selectedSide === "productive" && this.productiveHP > this.lazyHP);

      const lazyWinMessages = [
        "摆烂才是人生真谛！",
        "躺赢是最好的赢法！",
        "懒惰使人聪明，我赢了！",
      ];
      const lazyLoseMessages = [
        "输了也无所谓，继续躺...",
        "好累啊，不玩了...",
        "反正都是摆烂，赢不赢又有什么区别~",
      ];
      const productiveWinMessages = [
        "努力就会有回报！",
        "胜利属于奋斗者！",
        "日积月累，必有所成！",
      ];
      const productiveLoseMessages = [
        "失败是成功之母！",
        "我要加倍努力！",
        "下次我会表现得更好！",
      ];

      let messages;
      if (this.selectedSide === "lazy") {
        messages = isWinner ? lazyWinMessages : lazyLoseMessages;
      } else {
        messages = isWinner ? productiveWinMessages : productiveLoseMessages;
      }

      return messages[Math.floor(Math.random() * messages.length)];
    },
    confirmResult() {
      this.$emit("select-personality", this.selectedSide);
    },
    wait(ms) {
      return new Promise((resolve) => setTimeout(resolve, ms));
    },
    skipBattle() {
      // 直接结束战斗并显示结果
      this.currentPhase = "result";

      // 随机决定胜负，但让玩家有较高概率获胜
      const randomHP = Math.floor(Math.random() * 40) + 10;
      if (this.selectedSide === "lazy") {
        this.lazyHP = 60;
        this.productiveHP = randomHP;
      } else {
        this.productiveHP = 60;
        this.lazyHP = randomHP;
      }

      // 生成并保存MBTI类型和运势
      this.mbtiType = this.generateMBTIType();
      this.fortuneMessage = this.generateFortuneMessage();
      this.generateFortuneTags();
    },
    generateFortuneTags() {
      const tags = [
        "摸鱼达人",
        "职场高手",
        "躺平专家",
        "卷王之王",
        "佛系青年",
        "社交达人",
        "效率专家",
        "休息达人",
      ];

      // 随机选择3个标签
      this.fortuneTags = this.shuffleArray([...tags]).slice(0, 3);
    },
    getMBTIAnalysis() {
      const mbtiTraits = {
        lazy: {
          E: "在独处时更容易放松",
          I: "享受独处的摆烂时光",
          S: "注重当下的舒适感",
          N: "总能想出摆烂的新方法",
          T: "理性地选择最佳摆烂姿势",
          F: "追求内心的舒适与和谐",
          J: "计划性摆烂",
          P: "随性而为",
        },
        productive: {
          E: "在团队中更有动力",
          I: "独立专注的学习状态",
          S: "脚踏实地地完成目标",
          N: "创新的学习方法",
          T: "理性分析提高效率",
          F: "以热情驱动学习",
          J: "严格执行计划",
          P: "灵活应对变化",
        },
      };

      const mbtiTitles = {
        INTJ: "全知暗影",
        INTP: "灵魂黑客",
        ENTJ: "霸道总裁",
        ENTP: "神秘魔术师",
        INFJ: "闪电皮卡丘",
        INFP: "奶茶仙子",
        ENFJ: "团宠小太阳",
        ENFP: "彩虹独角兽",
        ISTJ: "守序扫地机器人",
        ISFJ: "暖心奶爸",
        ESTJ: "铁血教练",
        ESFJ: "学霸村长",
        ISTP: "熊猫侠",
        ISFP: "二次元主角",
        ESTP: "极限运动王",
        ESFP: "派对恶魔",
      };

      // 确保selectedSide已设置，如果未设置则默认为lazy
      const selectedType = this.selectedSide || "lazy";
      const traits = mbtiTraits[selectedType];
      const title = mbtiTitles[this.mbtiType] || "未知旅行者";

      // 防止mbtiType未设置的情况
      if (!this.mbtiType) {
        this.mbtiType = this.generateMBTIType();
      }

      // 使用保存的MBTI类型，而不是每次都重新生成
      return `你的MBTI类型倾向：${this.mbtiType}「${title}」\n
${traits.E}/${traits.I}\n
${traits.S}/${traits.N}\n
${traits.T}/${traits.F}\n
${traits.J}/${traits.P}\n
这种性格特质让你在${
        selectedType === "lazy" ? "摆烂" : "卷王"
      }的道路上独具特色。`;
    },
    generateMBTIType() {
      // 直接生成 MBTI 类型，不需要预定义数组
      let result = "";
      result += Math.random() > 0.5 ? "E" : "I";
      result += Math.random() > 0.5 ? "S" : "N";
      result += Math.random() > 0.5 ? "T" : "F";
      result += Math.random() > 0.5 ? "J" : "P";
      return result;
    },
    getDailyFortune() {
      // 返回保存的运势消息，而不是每次都重新生成
      return this.fortuneMessage;
    },
    generateFortuneMessage() {
      const fortunes = [
        "今日宜：摆烂、躺平、划水。忌：内卷、熬夜、赶ddl。",
        "王阳明曰：躺平时要躺得优雅，卷起来要卷得漂亮。",
        "今日运势：上班通勤遇堵车，老板突然放半天。事业上机会与挑战并存，建议摆烂中保持清醒。",
        "宜：刷视频、追剧、网购。忌：加班、开会、写报告。今日贵人方位：外卖小哥。",
        "早起困意三分在，摆烂精神一整天。适合发朋友圈晒美食，忌讳被老板抓包摸鱼。",
        "今日适合：假装很忙、优雅社交、云吸猫。小心：被工作找上门、被ddl追着跑。",
      ];

      return fortunes[Math.floor(Math.random() * fortunes.length)];
    },
    shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    },
    restartBattle() {
      this.currentPhase = "selection";
      this.selectedSide = null;
      this.currentRound = 1;
      this.lazyHP = 100;
      this.productiveHP = 100;
      this.currentAttacker = null;
      this.currentDefender = null;
      this.battleEnded = false;
      this.winner = null;
      this.showActionMessage = false;
      this.showDamage = false;
      this.currentDamage = 0;
      this.actionMessage = "";
      this.currentLazyDialog = "";
      this.currentProductiveDialog = "";
      this.showResult = false;
      this.fortuneTags = [];
      this.mbtiType = ""; // 重置MBTI类型
      this.fortuneMessage = ""; // 重置运势消息
    },
    getDialogMessage(side, type) {
      const dialogs = {
        lazy: {
          attack: [
            "摆烂也是一种力量！",
            "躺平即是正义！",
            "让我用懒散打败你！",
          ],
          defend: ["我要躺平防御！", "这么累干嘛...", "不要打扰我睡觉！"],
          normal: ["今天也是躺平的一天", "摆烂也要有格调", "效率那么重要吗？"],
        },
        productive: {
          attack: [
            "加油加油不要停！",
            "效率就是生产力！",
            "让我看看你的进取心！",
          ],
          defend: ["防御也要高效率！", "这点打击算什么！", "我还能更努力！"],
          normal: ["努力才会有收获", "今天又是充实的一天", "要对自己更有要求"],
        },
      };

      const messageList = dialogs[side][type];
      return messageList[Math.floor(Math.random() * messageList.length)];
    },
  },
};
</script>

<style scoped>
/* 移动优先设计 - 基础样式适用于手机 */
.battle-arena {
  background: var(--bg-card);
  border-radius: var(--border-radius-lg);
  padding: 1rem 0.8rem;
  margin: 0.5rem 0;
  box-shadow: var(--shadow-md);
  position: relative;
  overflow: hidden;
  border: 1px solid rgba(67, 97, 238, 0.2);
}

.battle-arena::before,
.battle-arena::after {
  content: "";
  position: absolute;
  width: 150px;
  height: 150px;
  border-radius: 50%;
  filter: blur(30px);
  opacity: 0.15;
  z-index: 0;
}

.battle-arena::before {
  top: -50px;
  right: -50px;
  background: var(--primary);
}

.battle-arena::after {
  bottom: -50px;
  left: -50px;
  background: var(--secondary);
}

.battle-title {
  text-align: center;
  font-size: 1.6rem;
  margin-bottom: 0.3rem;
  color: var(--text-primary);
  position: relative;
  width: 100%;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 1px;
  background: var(--primary-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
}

.battle-subtitle {
  text-align: center;
  font-size: 0.9rem;
  color: var(--text-secondary);
  margin-bottom: 1rem;
  width: 100%;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.battle-container {
  display: flex;
  align-items: stretch;
  justify-content: space-between;
  gap: 0.3rem;
  margin-bottom: 1rem;
  position: relative;
  z-index: 1;
}

.personality-card {
  flex: 1;
  background: var(--bg-light);
  border-radius: var(--border-radius-lg);
  padding: 0.8rem 0.4rem;
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
  height: 3px;
  z-index: 2;
}

.lazy-side::before {
  background: var(--primary-gradient);
}

.productive-side::before {
  background: var(--secondary-gradient);
}

.card-animation {
  height: 120px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: var(--border-radius-lg);
  margin-bottom: 0.8rem;
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
  width: 90px;
  height: 90px;
  object-fit: cover;
  border-radius: 50%;
  transition: var(--transition);
  z-index: 1;
  border: 2px solid transparent;
}

.lazy-side .character-img {
  border-color: var(--primary);
  box-shadow: 0 0 10px rgba(67, 97, 238, 0.3);
}

.productive-side .character-img {
  border-color: var(--secondary);
  box-shadow: 0 0 10px rgba(0, 245, 212, 0.3);
}

.image-decoration {
  position: absolute;
  z-index: 0;
  opacity: 0.3;
  filter: blur(1px);
  transition: var(--transition);
}

.image-decoration.left {
  width: 30px;
  height: 30px;
  left: 10px;
  top: 10px;
  border-radius: var(--border-radius-sm);
  background: var(--primary);
  transform: rotate(-15deg);
}

.image-decoration.right {
  width: 20px;
  height: 20px;
  right: 10px;
  bottom: 10px;
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
  font-size: 1.2rem;
  font-weight: 900;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: var(--shadow-lg), 0 0 10px rgba(67, 97, 238, 0.3);
  transition: var(--transition-bounce);
  text-transform: uppercase;
  letter-spacing: 1px;
  border: 2px solid var(--primary);
}

.character-name {
  font-size: 1.2rem;
  margin-bottom: 0.5rem;
  color: var(--text-primary);
  font-weight: 700;
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
  font-size: 0.8rem;
  color: var(--text-secondary);
  font-style: italic;
  padding: 0.6rem 0.4rem;
  border-radius: var(--border-radius-md);
  position: relative;
  line-height: 1.3;
  font-weight: 500;
  background: var(--bg-dark);
}

.lazy-side .character-quote {
  box-shadow: inset 0 0 10px rgba(67, 97, 238, 0.1);
}

.productive-side .character-quote {
  box-shadow: inset 0 0 10px rgba(0, 245, 212, 0.1);
}

.character-quote::before {
  content: '"';
  position: absolute;
  top: -3px;
  left: 8px;
  font-size: 1.5rem;
  font-family: serif;
  opacity: 0.2;
}

.lazy-side .character-quote::before {
  color: var(--primary);
}

.productive-side .character-quote::before {
  color: var(--secondary);
}

.battle-stats {
  display: grid;
  grid-template-columns: 1fr;
  gap: 0.2rem;
  padding: 0.5rem 0.3rem;
  background: var(--bg-dark);
  border-radius: var(--border-radius-md);
  margin-top: 0.8rem;
}

.stat-item {
  text-align: center;
}

.stat-name {
  font-size: 0.65rem;
  color: var(--text-secondary);
  margin-bottom: 0.1rem;
}

.stat-value {
  font-size: 0.8rem;
  font-weight: 600;
  color: var(--text-primary);
}

.personality-card.attacking {
  transform: translateX(10px) scale(1.02);
  z-index: 2;
}

.personality-card.defending {
  transform: translateX(-10px);
  opacity: 0.8;
}

.battle-actions {
  position: relative;
  height: 50px;
  margin-top: 0.8rem;
  text-align: center;
}

.action-message {
  font-size: 1.5rem;
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
  font-size: 2rem;
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
  font-size: 0.8rem;
  text-align: center;
  white-space: nowrap;
  font-weight: 600;
}

.battle-result {
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
  animation: fadeIn 0.5s ease;
  padding: 0.5rem;
  overflow-y: auto;
}

.result-content {
  background: var(--bg-card);
  padding: 1.5rem;
  border-radius: var(--border-radius-lg);
  text-align: center;
  animation: slideUp 0.5s ease;
  width: 100%;
  max-width: 90%;
  margin: 1rem auto 2rem;
  position: relative;
  border: 1px solid rgba(67, 97, 238, 0.2);
  box-shadow: var(--shadow-lg), 0 0 20px rgba(67, 97, 238, 0.2);
  overflow-y: auto;
  max-height: 85vh;
}

.result-content::before {
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

.result-title {
  font-size: 1.6rem;
  font-weight: 900;
  margin-bottom: 0.8rem;
  background: var(--primary-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  position: relative;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.result-message {
  font-size: 1rem;
  color: var(--text-secondary);
  margin-bottom: 1.5rem;
  line-height: 1.5;
}

.confirm-button {
  background: var(--primary-gradient);
  color: var(--text-primary);
  padding: 0;
  border-radius: var(--border-radius-lg);
  font-size: 1rem;
  font-weight: 600;
  transition: var(--transition);
  width: 100%;
  height: 3.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  text-transform: uppercase;
  letter-spacing: 1px;
  box-shadow: var(--shadow-neon);
  margin-top: 1rem;
}

.confirm-button:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-neon), var(--shadow-lg);
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

.battle-controls {
  position: absolute;
  top: 0.5rem;
  right: 0.5rem;
  display: flex;
  gap: 0.5rem;
  z-index: 10;
}

.skip-button {
  background: rgba(255, 255, 255, 0.1);
  color: var(--text-primary);
  padding: 0.4rem 0.8rem;
  border-radius: var(--border-radius-md);
  font-size: 0.8rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  transition: var(--transition);
}

.skip-button:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: translateY(-2px);
}

.skip-button svg {
  width: 14px;
  height: 14px;
}

.mbti-analysis {
  background: var(--bg-light);
  border-radius: var(--border-radius-lg);
  padding: 1rem;
  margin-top: 1rem;
  border: 1px solid rgba(67, 97, 238, 0.2);
}

.mbti-title {
  font-size: 1.1rem;
  font-weight: 600;
  color: var(--text-primary);
  margin-bottom: 0.8rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.mbti-title svg {
  width: 18px;
  height: 18px;
}

.mbti-content {
  color: var(--text-secondary);
  line-height: 1.4;
  font-size: 0.9rem;
  white-space: pre-line;
  text-align: left;
}

.fortune-analysis {
  background: var(--bg-light);
  border-radius: var(--border-radius-lg);
  padding: 1rem;
  margin-top: 1rem;
  border: 1px solid rgba(67, 97, 238, 0.2);
  position: relative;
  overflow: hidden;
}

.fortune-analysis::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 3px;
  background: var(--primary-gradient);
}

.fortune-title {
  font-size: 1.1rem;
  font-weight: 600;
  color: var(--text-primary);
  margin-bottom: 0.8rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.fortune-title svg {
  width: 18px;
  height: 18px;
}

.fortune-content {
  color: var(--text-secondary);
  line-height: 1.4;
  font-size: 0.9rem;
  text-align: left;
}

.fortune-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  margin-top: 0.8rem;
  justify-content: center;
}

.fortune-tag {
  background: rgba(67, 97, 238, 0.1);
  color: var(--primary);
  padding: 0.3rem 0.8rem;
  border-radius: 20px;
  font-size: 0.8rem;
}

/* HP相关样式 */
.hp-bar {
  position: absolute;
  top: 0.3rem;
  left: 0.3rem;
  right: 0.3rem;
  height: 6px;
  background: var(--bg-dark);
  border-radius: 3px;
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
  top: -1rem;
  right: 0;
  font-size: 0.7rem;
  color: var(--text-secondary);
}

/* 平板和桌面端优化 */
@media (min-width: 768px) {
  .battle-arena {
    padding: 1.5rem;
    margin: 1.5rem 0;
  }

  .battle-title {
    font-size: 2.5rem;
  }

  .battle-subtitle {
    font-size: 1.2rem;
    margin-bottom: 2rem;
  }

  .battle-container {
    gap: 2rem;
  }

  .personality-card {
    padding: 1.5rem;
  }

  .card-animation {
    height: 180px;
  }

  .character-img {
    width: 160px;
    height: 160px;
  }

  .character-name {
    font-size: 1.8rem;
  }

  .character-quote {
    font-size: 1.1rem;
    padding: 1rem 1.5rem;
  }

  .vs-badge {
    width: 70px;
    height: 70px;
    font-size: 1.8rem;
  }

  .battle-stats {
    grid-template-columns: repeat(2, 1fr);
    gap: 0.5rem;
    padding: 1rem;
  }

  .stat-name {
    font-size: 0.8rem;
  }

  .stat-value {
    font-size: 1.1rem;
  }

  .personality-card.attacking {
    transform: translateX(20px) scale(1.05);
  }

  .personality-card.defending {
    transform: translateX(-20px);
  }

  .action-message {
    font-size: 2.5rem;
  }

  .damage-number {
    font-size: 3rem;
  }

  .image-decoration.left {
    width: 60px;
    height: 60px;
  }

  .image-decoration.right {
    width: 40px;
    height: 40px;
  }

  .mbti-analysis,
  .fortune-analysis {
    padding: 1.5rem;
    margin-top: 1.5rem;
  }

  .mbti-title,
  .fortune-title {
    font-size: 1.3rem;
  }

  .mbti-content,
  .fortune-content {
    font-size: 1rem;
    line-height: 1.6;
  }

  .confirm-button {
    max-width: 300px;
    margin: 1.5rem auto 0;
  }
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

.game-recommends {
  margin-top: 1.5rem;
  background: var(--bg-light);
  border-radius: var(--border-radius-lg);
  padding: 1rem;
  border: 1px solid rgba(67, 97, 238, 0.2);
  position: relative;
  overflow: hidden;
}

.game-recommends::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 3px;
  background: var(--secondary-gradient);
}

.recommend-title {
  font-size: 1.1rem;
  font-weight: 600;
  color: var(--text-primary);
  margin-bottom: 1rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.recommend-title svg {
  width: 18px;
  height: 18px;
  color: var(--secondary);
}

.game-list {
  display: flex;
  flex-direction: column;
  gap: 0.8rem;
}

.game-card {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  background: var(--bg-card);
  border-radius: var(--border-radius-md);
  padding: 0.8rem;
  box-shadow: var(--shadow-sm);
  transition: var(--transition);
  position: relative;
  border: 1px solid rgba(67, 97, 238, 0.1);
}

.game-card:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

.game-icon {
  width: 50px;
  height: 50px;
  border-radius: 12px;
  overflow: hidden;
  flex-shrink: 0;
}

.game-icon img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.game-info {
  flex: 1;
  min-width: 0;
}

.game-name {
  font-size: 0.9rem;
  font-weight: 600;
  color: var(--text-primary);
  margin-bottom: 0.3rem;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.game-desc {
  font-size: 0.8rem;
  color: var(--text-secondary);
  margin-bottom: 0.5rem;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  line-height: 1.3;
}

.game-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.3rem;
}

.game-tag {
  font-size: 0.7rem;
  color: var(--secondary);
  background: rgba(0, 245, 212, 0.1);
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
}

.install-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0.5rem;
  background: var(--secondary-gradient);
  border-radius: var(--border-radius-md);
  color: var(--text-primary);
  font-size: 0.8rem;
  font-weight: 600;
  transition: var(--transition);
  box-shadow: var(--shadow-sm);
  flex-shrink: 0;
  width: 70px;
  gap: 0.3rem;
}

.install-btn svg {
  width: 14px;
  height: 14px;
}

.install-btn:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

/* 平板和桌面端优化 */
@media (min-width: 768px) {
  .game-recommends {
    padding: 1.5rem;
    margin-top: 2rem;
  }

  .recommend-title {
    font-size: 1.3rem;
    margin-bottom: 1.5rem;
  }

  .recommend-title svg {
    width: 22px;
    height: 22px;
  }

  .game-list {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 1rem;
  }

  .game-card {
    padding: 1rem;
  }

  .game-icon {
    width: 60px;
    height: 60px;
  }

  .game-name {
    font-size: 1rem;
  }

  .game-desc {
    font-size: 0.9rem;
  }

  .game-tag {
    font-size: 0.75rem;
  }

  .install-btn {
    padding: 0.6rem;
    font-size: 0.9rem;
    width: 80px;
  }

  .install-btn svg {
    width: 16px;
    height: 16px;
  }
}

/* 在结果弹窗中的游戏推荐样式调整 */
.result-game-recommends {
  margin-top: 1.5rem;
  background: transparent;
  border: none;
  position: relative;
  overflow: hidden;
  padding: 0;
}

.result-game-recommends::before {
  display: none;
}

.result-game-recommends .game-list {
  max-height: 300px;
  overflow-y: auto;
  scrollbar-width: thin;
  padding-right: 5px;
}

.result-game-recommends .game-list::-webkit-scrollbar {
  width: 5px;
}

.result-game-recommends .game-list::-webkit-scrollbar-thumb {
  background: rgba(67, 97, 238, 0.3);
  border-radius: 10px;
}

.result-game-recommends .game-card {
  margin-bottom: 0.5rem;
}

@media (min-width: 768px) {
  .result-game-recommends .game-list {
    max-height: 400px;
  }
}
</style>
