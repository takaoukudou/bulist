<template>
  <div>
    <div class="flexContainer">
      <v-text-field v-model="name" label="買うもの" class="input"></v-text-field>
      <v-btn @click="createTodo" class="mx-2 input" fab dark color="#4290FA">
        <v-icon dark>mdi-plus</v-icon>
      </v-btn>
    </div>
    <!-- <v-btn @click="createTodo">Create</v-btn> -->
    <List :itemList="todos"></List>
  </div>
</template>

<script>
import { API } from 'aws-amplify'
import { createTodo } from '~/src/graphql/mutations'
import { listTodos, listItemsSortedByStatus } from '~/src/graphql/queries'
import { onCreateTodo } from '~/src/graphql/subscriptions'
import { List } from "../components/List";

export default {
  data() {
    return {
      name: '',
      description: '',
      todos: [],
    }
  },
  created() {
    this.getTodos()
    this.subscribe()
    this.$nuxt.$on('reload', () => {
      this.getTodos();
    })
  },
  beforeDestroy () {
    this.$nuxt.$off('reload')
  },
  methods: {
    async createTodo() {
      const { name } = this
      const status = false
      if (!name) return false
      const item = { name ,status}
      await API.graphql({
        query: createTodo,
        variables: { input: item },
      })
      this.name = '';
      this.getTodos();
    },
    async getTodos() {
      const todos = await API.graphql({
        query: listTodos,
      })
      this.todos = todos.data.listTodos.items
      this.sort();
    },
    subscribe() {
      API.graphql({ query: onCreateTodo }).subscribe({
        next: (eventData) => {
          const todo = eventData.value.data.onCreateTodo
          if (this.todos.some((item) => item.name === todo.name)) return // remove duplications
          this.todos = [...this.todos, todo]
        },
      })
    },
    sort(){
        this.todos.sort(function(a,b){
            if(a.status < b.status) return -1;
            if(a.status > b.status) return 1;
            return 0;
        });
    }
  },
}
</script>

<style>
.flexContainer{
  /* font-size: 0; */
  display: flex;
}
.input{
  /* display: inline-block;
  font-size: 16px; */
}
</style>