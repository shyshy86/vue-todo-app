<template>
<div class="tdContainer">
    <ul class="tdList">
    <li class="tdItem" v-for="item in sortedTodos" :key="item.id" :class="getPriorityClass(item.priority)">
    <div class="tdItem-main">
      <div class="item-actions">
        <button @click="toggleTop(item)" class="top-btn" :class="{ active: item.isTop }">
          {{ item.isTop ? '取消置顶' : '置顶' }}
        </button>
        <span class="priority-badge" :class="item.priority">
          {{ getPriorityText(item.priority) }}
        </span>
      </div>
      <input type="checkbox" v-model="item.completed" class="tdToggle"/>
      <span class="tdTxt" :class="{completed:item.completed}">
        {{ item.text }}
      </span>
      <span class="create-time">
        {{ formatDate(item.createTime) }}
      </span>
    </div>
    <div class="tdItem-acts">
      <a @click="defTodo(item)">删除</a>
    </div>
    </li>
    </ul>
</div>
</template>

<script>
export default {
props:["todos"],
computed: {
  sortedTodos() {
    // 先按置顶排序，再按创建时间倒序
    return [...this.todos].sort((a, b) => {
      if (a.isTop && !b.isTop) return -1;
      if (!a.isTop && b.isTop) return 1;
      return new Date(b.createTime) - new Date(a.createTime);
    });
  }
},
methods: {
  defTodo(item){
    this.$emit("defTodo", item)
  },
  toggleTop(item) {
    this.$emit("toggleTop", item.id);
  },
  getPriorityClass(priority) {
    return `priority-${priority}`;
  },
  getPriorityText(priority) {
    const texts = { low: '低', medium: '中', high: '高' };
    return texts[priority];
  },
  formatDate(dateString) {
    const date = new Date(dateString);
    return `${date.getMonth()+1}/${date.getDate()} ${date.getHours()}:${date.getMinutes().toString().padStart(2, '0')}`;
  }
}
}
</script>

<style scoped>
.tdList {
    list-style: none;
    padding: 0;
    text-align: left;
    background-color: #fff;
    border-radius: 10px;
}

.tdItem {
    padding: 15px 20px 15px 15px;
    border-bottom: 1px solid #ddd;
    cursor: pointer;
    display: flex;
    justify-content: space-between;
    transition: all 0.3s ease;
}

.tdItem:last-child {
    border-bottom: 0;
}

.tdItem-main {
  display: flex;
  align-items: center;
  flex-grow: 1;
}

.item-actions {
  display: flex;
  align-items: center;
  margin-right: 15px;
}

.top-btn {
  padding: 4px 8px;
  background: #f0f0f0;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
  margin-right: 10px;
}

.top-btn.active {
  background: #ffd700;
  color: #333;
}

.priority-badge {
  padding: 2px 8px;
  border-radius: 10px;
  font-size: 12px;
  color: white;
  margin-right: 10px;
}

.priority-badge.low {
  background: #52c41a;
}

.priority-badge.medium {
  background: #faad14;
}

.priority-badge.high {
  background: #f5222d;
}

.priority-low {
  border-left: 3px solid #52c41a;
}

.priority-medium {
  border-left: 3px solid #faad14;
}

.priority-high {
  border-left: 3px solid #f5222d;
}

.tdToggle {
    cursor: pointer;
    margin-right: 15px;
}

.tdTxt {
    padding-left: 10px;
    flex-grow: 1;
}

.create-time {
  font-size: 12px;
  color: #999;
  margin-left: 15px;
}

.completed {
    text-decoration: line-through;
    color: #999;
}

.tdItem-acts {
    display: none;
    color: red;
}

.tdItem:hover .tdItem-acts {
    display: block;
}
</style>