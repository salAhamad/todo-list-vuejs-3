<template>
    <div class="todo-app-container">
        <div class="container" style="max-width: 750px;">
            <h3 class="mb-4 fw-bold text-center">My First ToDo Application in VueJs@3</h3>
            <hr />
            <div class="todo-input-block d-flex positive-relative">
                <input v-model="inputValue" :class="{'is-invalid': show }" class="form-control form-control-lg" type="text" placeholder="Enter Todo list to Add" />
                <button @click="closeEditing" :class="this.editedStatus !== null ? '' : 'd-none'" class="close-editing btn btn-outline-danger" role="button">&times;</button>
                <button @click="submitTask" class="btn btn-primary white-space-nowrap px-3 fw-bold">
                    {{this.editedStatus !== null ? 'Update ToDo List' : 'Add ToDo List'}} 
                </button>
            </div>
            <small v-show="show" class="text-danger input-error">
                Please, Enter the task <strong>Heading!</strong>
            </small>
            <hr />
            <div class="list-items border">
                <div class="list-row bg-primary text-white d-flex">
                    <h6 class="list-name mb-0 p-3 w-100">Task Name</h6>
                    <h6 class="list-status mb-0 p-3 ps-0 pe-2" style="min-width: 110px">Status</h6>
                    <h6 class="list-action mb-0 p-3 px-0" style="min-width: 90px">Action</h6>
                </div>
                <div 
                    v-for="(task, index) in tasksArray" 
                    :key="index" 
                    :class="{
                        'text-decoration-line-through': task.status === 'Completed',
                        'completed': task.status === 'Completed',
                        'in-progress': task.status === 'In-progress',
                        'editing-enabled': index === this.editedStatus,
                    }"
                    :data-index="index"
                    class="list-row d-flex">
                    <h6 class="list-name mb-0 p-3 w-100">{{task.name}}</h6>
                    <h6 
                        @click="changeStatus(index)" 
                        class="list-status mb-0 p-3 ps-0 pe-2" 
                        :class="{
                            'text-danger': task.status === 'to-do',
                            'text-warning': task.status === 'In-progress',
                            'text-success': task.status === 'Completed'
                        }"
                        style="min-width: 110px" role="button">
                        {{firstCharactor(task.status)}}
                    </h6>
                    <h6 class="list-action mb-0 py-2 p-0 d-flex" style="min-width: 90px">
                        <button @click="editTask(index)" class="py-1 px-2 me-2 btn btn-outline-primary">
                            <i class="fa-solid fa-pencil"></i>
                        </button>
                        <button @click="deleteTask(index)" class="py-1 px-2 btn btn-outline-danger">
                            <i class="fa-solid fa-trash-alt"></i>
                        </button>
                    </h6>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    
    export default {        
        name: 'TodoApp2',
        data() {
            return {
                inputValue: '',
                editedStatus: null,
                textEdited: null,
                availableStatuses: ['to-do', 'In-progress', 'Completed'],
                show: false,
                tasksArray: [],
                // tasksArray: [
                //     {
                //         name: 'Make To Do List',
                //         status: 'to-do'
                //     },
                //     {
                //         name: 'Check off first things on list',
                //         status: 'to-do'
                //     },
                //     {
                //         name: 'Ralize you are already accomplished 2 things',
                //         status: 'to-do'
                //     },
                //     {
                //         name: 'Reward yourslef with nap',
                //         status: 'to-do'
                //     },
                //     {
                //         name: 'Banana is the best fruit for weight gaining.',
                //         status: 'to-do'
                //     },
                // ]
            }    
        },
        mounted() {
            if (localStorage.todoList) {
                this.tasksArray = JSON.parse(localStorage.todoList);
            }
        },

        methods: {
            submitTask() {
                if(this.inputValue.length === 0) return this.show = true
                
                if(this.editedStatus === null) {
                    this.tasksArray.push({
                        name: this.inputValue,
                        status: 'to-do'
                    })
                    localStorage.setItem('todoList', JSON.stringify(this.tasksArray))
                } else {
                    this.taskEdited();
                }

                this.closeEditing();
                this.show = false
            },
            
            taskEdited() {
                if(this.textEdited !== this.inputValue) {
                    if(confirm('You have edited the task. \nAre you sure want to cancel?')) {
                        this.tasksArray[this.editedStatus].name = this.inputValue;
                        localStorage.setItem('todoList', JSON.stringify(this.tasksArray));
                        this.editedStatus = null;
                    } else {
                        this.closeEditing();
                    }
                }

            },

            editTask(index) {
                // this.taskEdited();
                this.inputValue = this.tasksArray[index].name;
                this.textEdited = this.tasksArray[index].name;
                this.editedStatus = index;
                this.show = false                
            },
            closeEditing() {
                this.editedStatus = null;
                this.inputValue = '';
            },

            deleteTask(index) {
                this.tasksArray.splice(index, 1)
                let allTaskData = this.tasksArray;
                console.table(allTaskData);
                localStorage.setItem('todoList', JSON.stringify(allTaskData))
            },

            changeStatus(index) {
                let newIndex = this.availableStatuses.indexOf(this.tasksArray[index].status);
                if(++newIndex > 2) newIndex = 0
                this.tasksArray[index].status = this.availableStatuses[newIndex];
                localStorage.setItem('todoList', JSON.stringify(this.tasksArray));
            },

            firstCharactor(str) {
                return str.charAt(0).toUpperCase() + str.slice(1);
            },
        }
    }
</script>

<style lang="scss" scoped>
    .todo-app-container {
        position: relative;
        width: 100%;
        min-height: 100vh;
        background-color: #e7f1f3;
        padding: 5vw;
    }
    .todo-input-block {
        gap: .75rem;
        .close-editing {
            font-size: 1rem * 1.75;
            line-height: 0;
            &:not(:hover) {
                background-color: #fff;
            }
        }
    }
    .white-space-nowrap { white-space: nowrap; }
    .list-items {
        position: relative;
        width: 100%;
        background-color: #fff;
        .list-row {
            position: relative;
            border-bottom: 1px solid #ccc;
            &:hover { background-color: #f1f1f1; }
            &.editing-enabled {
                background-color: rgba(#0d6efd, 0.1);
                box-shadow: 
                    inset 0 0 0 2px #ffffff,
                    inset 0 0 0 4px #0d6efd;
            }
            &.list-row-head {
                background-color: #607d8b;
                color: #fff;
            }
            .list-action span {
                min-width: 35px;
                background-color: #f5f5f5;
                border-radius: .25rem;
                cursor: pointer;
            }
            &.in-progress {background-color: #fffbea; }
            &.completed {background-color: #efffea; }
        }
    }
</style>
