<template>
  <div class="container py-5">
    <div class="w-6/12 border-2 rounded-xl border-gray-700 p-4 mx-auto my-5 flex flex-col items-center gap-4">
      <h1 class="font-bold text-gray-700 text-4xl text-center">Todo App</h1>
      <div class="w-3/4 flex items-center border-2 border-gray-500 gap-0.5">
        <input type="text" placeholder="Enter something..." class="w-full outline-none border-r-1 border-gray-500 p-2"
          v-model.trim="value" v-on:keypress.enter="addOrUpdate">
        <button v-if="currentTodoId === null" @click.enter="addTodo"
          class="w-12 h-10 border-gray-500 border-l-1">Add</button>
        <button v-else @click.enter="updateTodo" class="w-16 h-10 border-gray-500 border-l-1">Update</button>
      </div>
      <div class="w-3/4 flex items-center border-2 border-gray-500 gap-0.5">
        <input type="text" placeholder="Search something..." class="w-full outline-none border-r-1 border-gray-500 p-2"
          v-model.trim="search" @keyup="searchList">
      </div>
      <div class="w-3/4 flex flex-col">
        <div class="flex items-left gap-4 mt-4 mb-4">
          <div class="flex gap-1">
            <input @change="filterList(allTodoList)" name="filter" checked id="radio1" type="radio">
            <label for="radio1">All</label>
          </div>
          <div class="flex gap-1">
            <input @change="filterList(accepted)" name="filter" id="radio2" type="radio">
            <label for="radio2">Accepted</label>
          </div>
          <div class="flex gap-1">
            <input @change="filterList(notAccepted)" name="filter" id="radio3" type="radio">
            <label for="radio3">not Accepted</label>
          </div>
        </div>

        <div v-if="search.length">
          <div v-for="(item, index) in searchListTodo" :key="index" class="flex justify-between items-center my-1">
            <div class="flex items-center gap-3">
              <input @change="checkArray" type="checkbox" v-model="item.checked">
              <span :class="item.checked ? 'line-through' : ''">{{ item.title }}</span>
            </div>
            <div class="flex gap-1">
              <button @click="editTodo(item)" class="border rounded-md border-red-700 p-1 px-2">Edit</button>
              <button @click="removeTodo(item)" class="border rounded-md border-gray-700 p-1">Delete</button>
            </div>
          </div>
        </div>
        <div v-else-if="todoList.length && tabActive == null">
          <div v-for="(item, index) in todoList" :key="index" class="flex justify-between items-center my-1">
            <div class="flex items-center gap-3">
              <input @change="checkArray" type="checkbox" v-model="item.checked">
              <span :class="item.checked ? 'line-through' : ''">{{ item.title }}</span>
            </div>
            <div class="flex gap-1">
              <button @click="editTodo(item)" class="border rounded-md border-red-700 p-1 px-2">Edit</button>
              <button @click="removeTodo(item)" class="border rounded-md border-gray-700 p-1">Delete</button>
            </div>
          </div>
        </div>
        <div v-else-if="acceptedTodo.length && tabActive == true">
          <div v-for="(item, index) in acceptedTodo" :key="index" class="flex justify-between items-center my-1">
            <div class="flex items-center gap-3">
              <input @change="checkArray()" type="checkbox" v-model="item.checked">
              <span :class="item.checked ? 'line-through' : ''">{{ item.title }}</span>
            </div>
            <div class="flex gap-1">
              <button @click="editTodo(item)" class="border rounded-md border-red-700 p-1 px-2">Edit</button>
              <button @click="removeTodo(item)" class="border rounded-md border-gray-700 p-1">Delete</button>
            </div>
          </div>
        </div>
        <div v-else-if="notAcceptedTodo.length && tabActive == false">
          <div v-for="(item, index) in notAcceptedTodo" :key="index" class="flex justify-between items-center my-1">
            <div class="flex items-center gap-3">
              <input @change="checkArray()" type="checkbox" v-model="item.checked">
              <span :class="item.checked ? 'line-through' : ''">{{ item.title }}</span>
            </div>
            <div class="flex gap-1">
              <button @click="editTodo(item)" class="border rounded-md border-red-700 p-1 px-2">Edit</button>
              <button @click="removeTodo(item)" class="border rounded-md border-gray-700 p-1">Delete</button>
            </div>
          </div>
        </div>
        <div v-else>
          <h2 class="text-center text-xl">Empty</h2>
        </div>
        <div class="my-5 flex flex-col">
          <div v-if="todoList.length" @click="selectAll" class="flex gap-2">
            <input type="checkbox" name="selectAll" id="selectAll" v-model="selectAllTodo">
            <label @click="selectAll" for="selectAll">Select All</label>
          </div>
          <span>Accepted Todo: {{ countAcceptedTodo }}</span>
          <div class="flex items-center gap-2 mt-3">
            <button class="p-1 px-6 border-2 border-red-400 rounded-md mt-5" @click="removeChecked">Clear</button>
            <button class="p-1 px-6 border-2 border-red-400 rounded-md mt-5" @click="removeAll">Clear All</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      value: '',
      idCreater: 0,
      todoList: [],
      currentTodoId: null,
      selectAllTodo: false,
      dataFilter: null,
      accepted: true,
      notAccepted: false,
      allTodoList: null,
      acceptedTodo: [],
      notAcceptedTodo: [],
      tabActive: null,
      search: '',
      searchListTodo: []
    }
  },
  mounted() {
    this.idCreater = this.todoList.length
  },
  computed: {
    countAcceptedTodo() {
      return this.todoList.filter(el => el.checked === true).length
    }
  },
  methods: {
    searchList() {
      // this.searchListTodo = this.todoList.filter(res => res.title.toLowerCase().includes(this.search.toLowerCase()))
      this.searchListTodo = this.todoList.filter(res => res.title.toLowerCase().startsWith(this.search.toLowerCase()))
      console.log(this.searchListTodo)
    },
    filterList(item) {
      this.tabActive = item
      if (item == true) {
        this.acceptedTodo = this.todoList.filter(el => el.checked === true)
      } else if (item == false) {
        this.notAcceptedTodo = this.todoList.filter(el => el.checked === false)
      }
    },
    checkArray() {
      this.filterList(this.tabActive)
      // this.filter(this.tabActive)
    },
    addTodo() {
      if (this.value.length) {
        this.value = this.value[0].toUpperCase() + this.value.slice(1)
        this.todoList.push({
          id: this.idCreater,
          title: this.value,
          checked: false
        })
        this.value = ''
        this.idCreater++
        this.currentTodoList = this.todoList
      }
      this.checkArray(this.tabActive)
    },
    removeTodo(item) {
      const index = this.todoList.findIndex(el => el.id === item.id)
      this.todoList.splice(index, 1)
    },
    editTodo(item) {
      if (!this.value.length) {
        this.value = item.title
        this.currentTodoId = item.id
      }
    },
    updateTodo() {
      const index = this.todoList.findIndex(el => el.id === this.currentTodoId)
      this.todoList[index].title = this.value
      this.currentTodoId = this.value = null
    },
    addOrUpdate() {
      if (this.currentTodoId === null) {
        this.addTodo()
      } else {
        this.updateTodo()
      }
    },
    removeAll() {
      this.todoList = this.currentTodoList = []
    },
    removeChecked() {
      this.todoList = this.todoList.filter(el => el.checked === false)
    },
    selectAll() {
      this.selectAllTodo = !this.selectAllTodo
      if (this.selectAllTodo) {
        this.todoList.map(el => {
          el.checked = true
        })
        this.currentTodoList.map(el => {
          el.checked = true
        })
      } else {
        this.todoList.map(el => {
          el.checked = false
        })
        this.currentTodoList.map(el => {
          el.checked = false
        })
      }
      this.checkArray()
      // this.test(this.accTodo)
      // console.log('hello')
    }
  }
}
</script>

<style lang="scss">

</style>
