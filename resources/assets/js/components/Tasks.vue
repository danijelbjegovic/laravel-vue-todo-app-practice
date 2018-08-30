<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card card-default">
                    <div class="card-header">My Tasks</div>

                    <div class="card-body">


                        <div class="input-group">
                            <input type="text" class="form-control" v-model="task.name" @keydown.enter="create">
                            <span class="input-group-btn">
                                <button class="btn btn-success" @click="create">ADD</button>
                            </span>
                        </div>

                        <div class="tasks-list">
                            <div class="alert alert-danger" v-if="!tasks.length">
                                you have no tasks left
                            </div>
                            <ul class="list-unstyled">
                                <li v-for="task in tasks" :key="task.id" :class="{ done: task.completed} ">

                                    <div class="checkbox">

                                        <label>
                                            <input type="checkbox" v-model="task.completed" @click="done(task)">
                                            {{task.name}}
                                            <a class="pull-right" href="#" @click.prevent="remove(task)">x</a>
                                        </label>

                                    </div>
                                </li>
                            </ul>
                        </div>
                    </div>

                    <div class="card-footer" v-if="tasks.length">
                        <span class="badge badge-primary">You have {{ tasks.length }} task/s</span>
                        <span class="badge badge-warning">{{ remainingTasks() }} left</span>
                        <span class="badge badge-success">{{ completedTasks() }} completed</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        mounted() {
            this.fetchData()
        },
        data (){
            return {
                tasks: [],
                task: {
                    name: ''
                }
            }
        },
        methods: {
            fetchData (){
                axios.get('/api/tasks')
                    .then((res)=>{
                        this.tasks = res.data
                    })
                    .catch((err)=>{
                        console.log(err)
                    })
            },
            create(){
                axios.post('/api/tasks', this.task)
                    .then((res)=>{
                        this.tasks.unshift(res.data)
                        this.task.name = ''
                    })
                    .catch((err)=>{

                    })
            },
            done(task){
                axios.put(`/api/tasks/${task.id}`,{
                    completed: !task.completed
                })
                .then((res)=>{
                    console.log('task updated');
                })
                .catch((err)=>{
                    console.log(err);
                })
            },
            remove(task){
                axios.delete(`/api/tasks/${task.id}`)
                .then((res)=>{
                    const taskIndex = this.tasks.indexOf(task)
                    this.tasks.splice(taskIndex, 1)
                })
                .catch((err)=>{
                    console.log(err);
                })
            },
            remainingTasks(){
                return this.tasks.filter(task=>{return !task.completed})
                    .length
            },
            completedTasks(){
                return this.tasks.filter(task=>{return task.completed})
                    .length
            }
        }
    }
</script>
<style>
    body, .tasks-list{
        padding-top: 20px;
    }
    .done label {
        text-decoration: line-through;
    }
</style>