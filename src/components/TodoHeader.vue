<template>
<div class="hdContainer">
  <h1 class="hdTitle">待办事项</h1>
  <input 
    class="newTodo"
    placeholder="请输入您的待办事项，按下回车后即可添加"
    v-model.trim="newTodo"
    @keyup.enter="addNewTodo"
  />
  <div class="priority-selector">
    <label>紧急程度：</label>
    <select v-model="selectedPriority">
      <option value="low">低</option>
      <option value="medium">中</option>
      <option value="high">高</option>
    </select>
  </div>
  <p class="hdMsg" v-show="isShowMsg">
    最多输入{{ countLimit }}个字符!!!
  </p>
</div>
</template>

<script>
const WORD_COUNT_LIMIT = 50;
export default {
  data(){
    return {
      newTodo:"",
      countLimit:WORD_COUNT_LIMIT,
      selectedPriority: "medium" // 默认中等优先级
    }
  },
  computed:{
    isShowMsg(){
      return this.newTodo.length > WORD_COUNT_LIMIT;
    }
  },
  methods:{
    addNewTodo(){
      if(this.newTodo.length == 0 || this.newTodo.length > WORD_COUNT_LIMIT){
        return ;
      }
      // 传递任务对象，包含文本和优先级
      this.$emit("addTodo", {
        text: this.newTodo,
        priority: this.selectedPriority
      });
      this.newTodo = "";
      this.selectedPriority = "medium"; // 重置为默认值
    }
  }
}
</script>

<style scoped>
.hdContainer {
  text-align: center;
  font-size: 16px;
}

.hdTitle {
  color: #4e6ef2;
  font-size: 2.5em;
  margin-bottom: 20px;
}

.newTodo {
  width: 100%;
  padding: 20px 20px;
  border: none;
  border-radius: 10px;
  font-size: 16px;
  box-sizing: border-box;
  margin-bottom: 15px;
}

.newTodo:focus {
  outline-color: #4e6ef2;
}

.priority-selector {
  margin-bottom: 15px;
}

.priority-selector label {
  margin-right: 10px;
}

.priority-selector select {
  padding: 8px 12px;
  border-radius: 5px;
  border: 1px solid #ddd;
}

.hdMsg {
  color: red;
  margin: 10px 0;
}
</style>