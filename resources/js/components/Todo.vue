<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">Todo List</div>

                    <div class="card-body">
                        <div class="input-group">
                            <input type="text" class="form-control" aria-label="todo" aria-describedby="todo" v-model="todo_input">
                            <div class="input-group-append">
                                <button v-if="!edit_todo_id" type="button" class="btn btn-info text-white" @click="saveTodo()">Add</button>

                                <button v-else type="button" class="btn btn-info text-white" @click="updateTodo()">Update</button>
                            </div>
                        </div>

                    

                        <table class="table mt-5">
                            <tr>
                                <th>Sl.</th>
                                <th>Name</th>
                                <th>Action</th>
                            </tr>
                            <tr v-for="(todo, index) in todos" :key="index">
                                <td>{{todo.id}}</td>
                                <td>{{todo.name}}</td>
                                <td>
                                    <button type="button" class="btn btn-sm btn-danger" @click="deleteTodo(todo.id)">Delete</button>

                                    <button type="button" class="btn btn-sm btn-info" @click="editTodoCustom(todo.id)">Edit</button>
                                </td>
                            </tr>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
// import Vue from 'vue'   // in Vue 2
import * as Vue from 'vue' // in Vue 3
import axios from 'axios'
import VueAxios from 'vue-axios'

    export default {
        data(){
            return{
                todos:[],
                api: 'http://localhost:8000/api/todo',
                todo_input:"",
                edit_todo_id:"",
                edit_index: ""
            }
        },
        mounted() {
            this.getTodo();
            /* axios.get(this.api).then(res=>{
                this.todos = res.data.data;
                //console.log(res.data.data);
            }) */
        },
        methods:{
            async getTodo(){
                await axios.get(this.api).then(res=>{
                    this.todos = res.data.data;
                }).catch(error=>{
                    console.log(error)
                    this.todos = []
                })
            },
            saveTodo(){
               if( this.todo_input.length > 0){
                const data = {"name": this.todo_input};
                axios.post(this.api, data).then(res=>{
                this.todos.push(res.data.data);
                this.todo_input = ""
                console.log(res.data.msg);
                })  
               }
            },
            editTodo(index){
                if(this.todos[index].id){
                    this.todo_input = this.todos[index].name;
                    this.edit_todo_id = this.todos[index].id;
                    this.edit_index = index;
                }
            },
            editTodoCustom(id){
                axios.get(this.api+"/"+id).then(res=>{
                    this.todo_input = res.data.data.name;
                    this.edit_todo_id = res.data.data.id;
                })
            },
            updateTodo(){
                if( this.todo_input.length > 0){
                    const data = {"name": this.todo_input};
                    axios.put(this.api+"/"+this.edit_todo_id, data).then(res=>{
                        this.todo_input = "";
                        this.edit_todo_id = "";
                        this.getTodo();
                    })  
               }
            },
            deleteTodo(id){
                axios.delete(this.api+"/"+id).then(res=>{
                    this.getTodo();
                })
            },
        }
    }
</script>
