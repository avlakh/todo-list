<template>
    <div class="container">
        <h2 class="text-center mt-5 mb-5">My Simple To-do App</h2>
        <div class="row">
            <!-- Input -->
            <div class="col-sm d-flex justify-content-center align-items-start" >
                <div class="d-flex gap-3">
                    <input 
                        v-model="task"
                        v-on:keyup.enter="submitTask"
                        type="text" 
                        class="form-control" 
                        placeholder="Enter task"/>
                    <button @click="submitTask" class="btn btn-success">Submit</button>
                </div>
            </div>
            <!-- Task table -->
            <div class="col-sm">
                <div class="table-responsive-sm">
                    <table class="table table-bordered">
                        <thead>
                            <tr>
                                <th>Task</th>
                                <th>Status</th>
                                <th class="text-center">#</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(task, index) in tasks" :key="index">
                                <td>
                                    <span :class="{'finished': task.status === 'finished'}">
                                        {{ task.name }}
                                    </span>
                                </td>
                                <td class="status">
                                    <span 
                                        class="pointer" 
                                        @click="changeStatus(index)"
                                        :class="
                                        {
                                            'text-danger': task.status === 'to-do',
                                            'text-warning': task.status === 'in-progress',
                                            'text-success': task.status === 'finished'
                                        }">
                                        {{ firstCharUpper(task.status) }}
                                    </span>
                                </td>
                                <td>
                                    <div class="d-flex justify-content-center gap-3">
                                        <div class="text-center" @click="editTask(index)">
                                            <span class="fa fa-pen"></span>
                                        </div>
                                        <div class="text-center" @click="deleteTask(index)">
                                            <span class="fa fa-trash"></span>
                                        </div>
                                    </div>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'TodoApp',
    data() {
        return {
            task: '',
            editedTask: null,
            availableStatus: ['to-do', 'in-progress', 'finished'],
            tasks: []
        }
    },
    created() {
        this.tasks = JSON.parse(localStorage.getItem('tasks')) || [];
    },
    methods: {
        submitTask(){
            if (this.task.length === 0) return;

            if (this.editedTask === null) {
                this.tasks.push({
                    name: this.task,
                    status: 'to-do'
                });
            } else {
                this.tasks[this.editedTask].name = this.task;
                this.editedTask = null;
            }

            this.task = '';
            localStorage.setItem('tasks', JSON.stringify(this.tasks));
        },
        deleteTask(index){
            this.tasks.splice(index, 1);
            localStorage.setItem('tasks', JSON.stringify(this.tasks));
        },
        editTask(index){
            this.task = this.tasks[index].name;
            this.editedTask = index;
        },
        changeStatus(index){
            let newIndex = this.availableStatus.indexOf(this.tasks[index].status);
            if (++newIndex > 2) newIndex = 0;
            this.tasks[index].status = this.availableStatus[newIndex];
            localStorage.setItem('tasks', JSON.stringify(this.tasks));
        },
        firstCharUpper(str){
            return str.charAt(0).toUpperCase() + str.slice(1);
        }
    }
}
</script>

<style scoped>

.pointer {
    cursor: pointer;
}

.finished {
    text-decoration: line-through;
}

.status {
    width: 120px;
}

@media screen and (max-width: 600px){
    .row {
        gap: 50px;
    }
}
</style>