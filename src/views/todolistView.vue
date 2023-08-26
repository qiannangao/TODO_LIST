<template>
  <section class="todoapp">
    <header class="header">
      <h1>todos</h1>
      <input class="new-todo" placeholder="What needs to be done?" autofocus @keyup.enter="addTodo">
    </header>
    <!-- This section should be hidden by default and shown when there are todos -->
    <section class="main">
      <input id="toggle-all" class="toggle-all" type="checkbox" @change="allChecked">
      <label for="toggle-all">Mark all as complete</label>
      <ul class="todo-list">
        <!-- These are here just to show the structure of the list items -->
        <!-- List items should get the class `editing` when editing and `completed` when marked as completed -->
        <li :class="{completed:item.done,editing:editId===item.id}" v-for="item in newList" :key="item.id">
          <div class="view">
            <input class="toggle" type="checkbox" v-model="item.done">
            <label @dblclick="editItems($event,item)">{{ item.todo }}</label>
            <button class="destroy" @click="delItems(item.id)"></button>
          </div>
          <input class="edit" :value="item.todo" @keyup.enter="updateItems" @keyup.esc="cancelEditItems" @blur="cancelEditItems">
        </li>
        <!-- <li>
          <div class="view">
            <input class="toggle" type="checkbox">
            <label>Buy a unicorn</label>
            <button class="destroy"></button>
          </div>
          <input class="edit" value="Rule the web">
        </li> -->
      </ul>
    </section>
    <!-- This footer should be hidden by default and shown when there are todos -->
    <footer class="footer">
      <!-- This should be `0 items left` by default -->
      <span class="todo-count"><strong>{{ leftItems }}</strong> item left</span>
      <!-- Remove this if you don't implement routing -->
      <ul class="filters">
        <li>
          <a :class="flag==='all'?'selected':''" href="#/" @click.prevent="filterItems('all')">All</a>
        </li>
        <li>
          <a :class="flag==='active'?'selected':''" href="#/active" @click.prevent="filterItems('active')">Active</a>
        </li>
        <li>
          <a :class="flag==='completed'?'selected':''" href="#/completed" @click.prevent="filterItems('completed')">Completed</a>
        </li>
      </ul>
      <!-- Hidden if no completed items are left ↓ -->
      <button class="clear-completed" @click="clearItems">Clear completed</button>
    </footer>
  </section>
  <footer class="info">
    <p>Double-click to edit a todo</p>
    <!-- Remove the below line ↓ -->
    <p>Template by <a href="http://sindresorhus.com">Sindre Sorhus</a></p>
    <!-- Change this out with your name and url ↓ -->
    <p>Created by <a href="http://todomvc.com">you</a></p>
    <p>Part of <a href="http://todomvc.com">TodoMVC</a></p>
  </footer>
</template>

<script setup>
import { computed, reactive, ref,nextTick } from 'vue'
const todoList = reactive({
  list: [{
    id: 1,
    todo: '示例1',
    done: false
  },
  {
    id: 2,
    todo: '示例2',
    done: true
  }]
})
const addTodo = (e) => {
  console.log(e);
  if (e.target.value) {
    todoList.list.push({
      id: Math.floor(Math.random() * 100),
      todo: e.target.value,
      done: false
    })
    e.target.value = ''

  } else {
    alert('需填写')
  }

}
// 全选
const allChecked = (e) => {
  console.log(e.target.checked);
  todoList.list.forEach(element => {
    element.done = e.target.checked
  });
}
// 剩余多少项
const leftItems = computed(() => {
  return todoList.list.filter(item => !item.done).length
})
// 清空已完成
const clearItems = () => {
  todoList.list = todoList.list.filter(item => !item.done)
}
const flag = ref('all')
// 点击全部/已完成/未完成
const filterItems = (type) => {
  if (type === 'all') {
    flag.value = 'all'
  } else if (type === 'active') {
    flag.value = 'active'
  } else if (type === 'completed') {
    flag.value = 'completed'
  }
}

// 页面渲染的数据需使用计算属性过滤后
const newList = computed(() => {
  if (flag.value === 'active') {
    return todoList.list.filter(item => !item.done)
  } else if (flag.value === 'completed') {
    return todoList.list.filter(item => item.done)
  }else{
    return todoList.list
  }
})
const editId=ref('')
// 双击编辑
const editItems=async (e,item)=>{
  if(item.done){
    alert('已完成事件不能继续编辑')
    return
  }
  editId.value=item.id
  // dom更新时机
  // Vue 会在“next tick”更新周期中缓冲所有状态的修改，以确保不管你进行了多少次状态修改，每个组件都只会被更新一次。
  await nextTick();
  console.log(e.target.parentNode.nextSibling);
  e.target.parentNode.nextSibling.focus()
}
// 更新数据
const updateItems=(e)=>{
  const item=todoList.list.find(item=>item.id===editId.value)
  if(item){
    item.todo=e.target.value
  }
  editId.value=''
}
// 取消编辑
const cancelEditItems=()=>{
  editId.value=''
}
// 删除
const delItems=(id)=>{
 todoList.list= todoList.list.filter(item=>item.id!=id)
}
</script>

<style lang="scss">
@import url("@/style/base.scss");
@import url("@/style/index.scss");
</style>