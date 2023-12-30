<script setup>
import { useRoute, RouterLink } from 'vue-router';
import { onMounted, ref } from 'vue';
import {useTodoStore} from '../stores/todo'

import Loading from '../components/Loading.vue'

const todoStore = useTodoStore()
const todoId = ref(-1)
const isLoaded = ref(false)
const isUpdated = ref(false)
const route = useRoute()

onMounted( async () => {
    //console.log(route.params.id)
    await todoStore.loadTodo(route.params.id)
    isLoaded.value = true
    todoId.value = parseInt(route.params.id) 
    //console.log(todoStore.selectedTodo)
})
const editTodo = async (selectedTodo) =>{
    try {
        isLoaded.value = false
        const bodyData = {
            name : selectedTodo.name,
            status : selectedTodo.status
        }
    await todoStore.editTodo(bodyData, todoId.value)
    isUpdated.value = true
    isLoaded.value = true
    setTimeout(() => {
        isUpdated.value = false
    }, 3000)
    } catch (error) {
        console.log('error',error)
    }
    
}
</script>

<template>
    <div class="w-1/2 mx-auto">
        <div class="toast toast-top toast-start">
            <div v-if="isUpdated" class="alert alert-success">
                <span>Update successfully.</span>
            </div>
        </div>
        <div class="flex items-center">
            <RouterLink :to="{name:'todo-list'}">
                <svg xmlns="http://www.w3.org/2000/svg" height="16" width="16" viewBox="0 0 512 512"><path d="M177.5 414c-8.8 3.8-19 2-26-4.6l-144-136C2.7 268.9 0 262.6 0 256s2.7-12.9 7.5-17.4l144-136c7-6.6 17.2-8.4 26-4.6s14.5 12.5 14.5 22l0 72 288 0c17.7 0 32 14.3 32 32l0 64c0 17.7-14.3 32-32 32l-288 0 0 72c0 9.6-5.7 18.2-14.5 22z"/></svg>
            </RouterLink>
            <div class="ml-2">Edit Page</div>
        </div>
        <div v-if="isLoaded">
        ID : <div class="badge badge-neutral">{{ todoId }}</div>
            <label class="form-control w-full">
                <div class="label">
                    <span class="label-text">Name</span>
                </div>
                <input type="text" 
                placeholder="Type name todo" 
                class="input input-bordered w-full" 
                v-model="todoStore.selectedTodo.name" 
                />
            </label>
            
            <label class="form-control w-full">
                <div class="label">
                    <span class="label-text">Status</span>
                </div>
                <select class="select select-bordered" v-model="todoStore.selectedTodo.status">
                    <option value="">Select Status</option>
                    <option v-for="status in todoStore.statuses" :value="status">
                        {{ status }}
                    </option>
                </select> 
            </label>
            <div class="flex">
                <button class="btn btn-primary w-full mt-4" @click="editTodo(todoStore.selectedTodo)">Edit</button>
            </div>
        </div>
        <div v-else>
           <Loading></Loading>
        </div>
    </div>
</template>


<style scoped>
svg {
    fill: white;
}
</style>