<template>
  <div id="app" class="container">
    <div class="loading" v-if="loading">加载中...</div>
    <div v-else>
      <TodoHeader @addTodo="addTodo"></TodoHeader>
      <TodoList 
        :todos="filteredTodoList" 
        @defTodo="delTodo"
        @toggleTop="toggleTop"
      ></TodoList>
      <TodoFooter 
        :tabType="tabType" 
        :count="filteredTodoList.length" 
        @changeTabType="changeTabType"
      ></TodoFooter>
    </div>
  </div>
</template>

<script>
import TodoHeader from './components/TodoHeader.vue'
import TodoList from './components/TodoList.vue'
import TodoFooter from './components/TodoFooter.vue';

// 模拟API调用
const api = {
  async getTodos() {
    // 模拟网络延迟
    return new Promise(resolve => {
      setTimeout(() => {
        const saved = localStorage.getItem("vue-todos");
        resolve(saved ? JSON.parse(saved) : []);
      }, 500);
    });
  },
  
  async saveTodos(todos) {
    return new Promise(resolve => {
      setTimeout(() => {
        localStorage.setItem("vue-todos", JSON.stringify(todos));
        resolve();
      }, 300);
    });
  }
};

export default {
  components: {
    TodoHeader,
    TodoList,
    TodoFooter
  },
  data() {
    return {
      todos: [],
      tabType: 0,
      loading: true
    }
  },
  computed: {
    filteredTodoList() {
      if (this.tabType == 0) {
        return this.todos;
      }
      const tabType = this.tabType;
      return this.todos.filter(function(item) {
        if (tabType == 1) return !item.completed;
        return item.completed;
      });
    }
  },
  methods: {
    async addTodo(todoData) {
      console.log("新增加的任务信息为：", todoData);
      const newTodo = {
        id: Date.now(),
        text: todoData.text,
        completed: false,
        priority: todoData.priority,
        isTop: false,
        createTime: new Date().toISOString()
      };
      
      this.todos.push(newTodo);
      await this.saveToServer();
    },
    
    async delTodo(item) {
      console.log("要删除的任务id为" + item.id);
      this.todos = this.todos.filter(todo => todo.id !== item.id);
      await this.saveToServer();
    },
    
    changeTabType(type) {
      this.tabType = type;
    },
    
    async toggleTop(todoId) {
      const todo = this.todos.find(t => t.id === todoId);
      if (todo) {
        todo.isTop = !todo.isTop;
        await this.saveToServer();
      }
    },
    
    // 从服务器加载数据
    async loadFromServer() {
      try {
        this.loading = true;
        this.todos = await api.getTodos();
        console.log('加载数据成功');
      } catch (error) {
        console.error('加载数据失败:', error);
      } finally {
        this.loading = false;
      }
    },
    
    // 保存数据到服务器
    async saveToServer() {
      try {
        await api.saveTodos(this.todos);
        console.log('保存数据成功');
      } catch (error) {
        console.error('保存数据失败:', error);
      }
    }
  },
  
  // 监听todos变化，自动保存（防抖处理）
  watch: {
    todos: {
      handler: function() {
        // 防抖，避免频繁保存
        if (this.saveTimeout) clearTimeout(this.saveTimeout);
        this.saveTimeout = setTimeout(() => {
          this.saveToServer();
        }, 1000);
      },
      deep: true
    }
  },
  
  // 组件挂载时加载数据
  async mounted() {
    await this.loadFromServer();
  }
}
</script>

<style>
html, body {
  background-color: #f5f5f5;
  margin: 0;
  padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.container {
  max-width: 980px;
  min-height: 100vh;
  margin: 0 auto;
  padding: 0 20px;
  box-sizing: border-box;
}

.loading {
  text-align: center;
  padding: 50px;
  font-size: 18px;
  color: #666;
}
</style>