<template>
  <main class="app">
    <!-- 인사 -->
    <section class="greeting">
      <h1 class="title">
        What's Up!!<input type="text" placeholder="이름 입력!" v-model.lazy.trim="name">
        <!-- {{ name }} -->
      </h1>
    </section>
    <!-- todo create -->
    <section class="create_todo">
      <h2>create todo</h2>
      <!-- @:submit.prevent - 새로고침되는 현상 방지 -->
      <form id="new-todo-form" @:submit.prevent="addTodo">
        <h4>할 일을 입력해주세요!</h4>
        <input type="text" placeholder="할 일을 입력해주세요!" class="todo_input" v-model.lazy.trim="input_content">
        <!-- {{ input_content }} -->

        <div class="options">
          <label class="label">
            <input type="radio" name="category" id="category1" value="business" v-model="input_category">
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label class="label">
            <input type="radio" name="category" id="category2" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
          <!-- 선택한 카테고리 - {{ input_category }} -->
        </div>

        <input type="submit" value="Add Todo">
      </form>
    </section>
    <!-- todo list -->
    <section class="todo_list">
      <h3>Todo List</h3>
      <!-- {{ todos }}
      {{ todos_asc }} -->
      <div class="list">
        <div :class="`todo_item ${todo.done && 'done'}`" v-for="todo in todos_asc" :key="todo.createAt">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo_contents">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">delete</button>
          </div>
        </div>
      </div>
    </section>

  </main>
</template>

<script setup>
import { ref, watch, onMounted, computed } from 'vue';

  const name = ref('');
  const input_content = ref('');//입력내용
  const input_category = ref('');
  const todos = ref([]);

  // 삭제함수
  const removeTodo = (item) => {
    todos.value = todos.value.filter((aa)=>aa !== item)
  }
  // 시간 순으로 정리(최신이 위에 오게)
  const todos_asc = computed(()=>todos.value.sort((a,b)=>b.createAt-a.createAt))

  // 할 일 입력값들을 받아오는 함수
  const addTodo = ()=>{
    //console.log("할일입력값 받아오는 함수실행")
    // 입력값이 없거나 카테고리를 선택하지 않았을땐 코드블록 밖으로 빠져나감
    if(input_content.value.trim() === '' || input_category.value === null){
      return
    }

    todos.value.push({
      content:input_content.value,
      category:input_category.value,
      done:false
    })
    // 입력 후 초기화
    input_content.value = '';
    input_category.value = null;
  }

  // todos가 바뀌는 것 감지
  watch(todos,(newName)=>{
    //console.log("투두스 감지", newName);
    localStorage.setItem('todos', JSON.stringify(newName))
  },{deep:true})
  // deep - 속성의 프로퍼티나 하위 뎁스도 감지

  // 이름 입력을 감지하고 로컬스토리지에 저장, 업데이트
  watch(name,(newName)=>{
    localStorage.setItem('name', newName)
  })

  // 새로 열었을때 로컬스토리지에서 불러옴(없을때는 비워놓음)
  onMounted(()=>{
    name.value = localStorage.getItem('name') || '';
    todos.value = JSON.parse(localStorage.getItem('todos') || []);
    //console.log("투두스 불러옴", todos.value);
  });
</script>
