<template>
  <section class="collection-display">
    <h2 class="collection-title">人格卡片</h2>
    <p v-if="collection.length === 0" class="empty-collection">
      你的收藏是空的。提交你的周末计划来发现隐藏人格！
    </p>

    <div class="card-grid" v-else>
      <div
        v-for="card in collection"
        :key="card.id"
        class="collection-card"
        :class="getRarityClass(card.rarity)"
        @click="viewCard(card)"
      >
        <div class="card-badge" v-if="isNewCard(card)">新!</div>
        <div class="card-image">
          <img :src="card.image" :alt="card.name" />
        </div>
        <div class="card-name">{{ card.name }}</div>
        <div class="card-rarity">{{ formatRarity(card.rarity) }}</div>
      </div>
    </div>

    <div class="collection-share" v-if="collection.length > 0">
      <button class="btn btn-secondary" @click="shareCollection">
        分享收藏
      </button>
    </div>
  </section>
</template>

<script>
export default {
  name: "CollectionDisplay",
  props: {
    collection: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      newCards: new Set(), // 追踪新添加的卡片
    };
  },
  watch: {
    collection: {
      handler(newCollection, oldCollection) {
        // 检查新卡片
        if (oldCollection && newCollection.length > oldCollection.length) {
          const newCardIds = newCollection
            .filter(
              (card) => !oldCollection.some((oldCard) => oldCard.id === card.id)
            )
            .map((card) => card.id);

          // 添加到新卡片集合
          newCardIds.forEach((id) => this.newCards.add(id));

          // 1分钟后移除"新"标签
          setTimeout(() => {
            newCardIds.forEach((id) => this.newCards.delete(id));
          }, 60000);
        }
      },
      deep: true,
    },
  },
  methods: {
    getRarityClass(rarity) {
      return `rarity-${rarity}`;
    },
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
    isNewCard(card) {
      return this.newCards.has(card.id);
    },
    viewCard(card) {
      this.$emit("view-card", card);
    },
    shareCollection() {
      // 在真实应用中，这里会生成可分享的图片或链接
      const shareText = `我已经收集了${this.collection.length}个周末人格！查看我的收藏，访问周末人格分裂.com`;

      if (navigator.share) {
        navigator
          .share({
            title: "我的周末人格收藏",
            text: shareText,
            url: window.location.href,
          })
          .catch((err) => {
            alert("分享失败: " + err);
          });
      } else {
        // 对不支持Web Share API的浏览器的备选方案
        alert("分享这段文字:\n\n" + shareText);
      }
    },
  },
};
</script>

<style scoped>
.collection-display {
  margin-bottom: 2rem;
}

.collection-title {
  font-size: 1.75rem;
  margin-bottom: 1rem;
  color: #8b5cf6;
  text-align: center;
}

.empty-collection {
  text-align: center;
  padding: 2rem;
  background-color: white;
  border-radius: 8px;
  color: #718096;
}

.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.collection-card {
  background-color: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  cursor: pointer;
  transition: all 0.2s ease;
  position: relative;
  border: 2px solid transparent;
}

.collection-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
}

.card-image {
  height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f7fafc;
  overflow: hidden;
}

.card-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.card-name {
  padding: 0.5rem;
  font-weight: bold;
  text-align: center;
  font-size: 0.875rem;
  color: #2d3748;
}

.card-rarity {
  padding: 0.25rem 0.5rem;
  font-size: 0.75rem;
  background-color: #f7fafc;
  color: #718096;
  text-align: center;
}

.card-badge {
  position: absolute;
  top: -5px;
  right: -5px;
  background-color: #f15bb5;
  color: white;
  font-size: 0.75rem;
  font-weight: bold;
  padding: 0.25rem 0.5rem;
  border-radius: 10px;
  z-index: 1;
  animation: bounce 1s ease infinite;
}

@keyframes bounce {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-5px);
  }
}

/* 稀有度样式 */
.rarity-common {
  border-color: #e2e8f0; /* 白色/银色 */
}

.rarity-rare {
  border-color: #3b82f6; /* 蓝色 */
}

.rarity-epic {
  border-color: #8b5cf6; /* 紫色 */
}

.rarity-legendary {
  border-color: #f59e0b; /* 金色 */
  box-shadow: 0 0 15px rgba(245, 158, 11, 0.3);
}

.collection-share {
  display: flex;
  justify-content: center;
  margin-top: 1rem;
}

@media (max-width: 600px) {
  .card-grid {
    grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
  }

  .card-image {
    height: 80px;
  }

  .card-name {
    font-size: 0.75rem;
  }
}
</style>
