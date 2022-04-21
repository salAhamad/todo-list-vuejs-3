<template>
  <div class="todo-app-container">
    <div class="container">
      <h2 class="text-center mb-4">My Todo List(s)</h2>
      <div class="input-container d-flex mb-4">
        <input v-model="inputValue" class="form-control form-control-lg" type="text" placeholder="Enter Todo list to Add" />
        <button @click="submitTask" class="btn btn-warning px-4 ms-3">Submit</button>
      </div>
      <div class="doto-item">
        <table class="table table-bordered bg-white align-middle">
          <thead class="table-dark">
            <tr>
              <th scope="col">#</th>
              <th scope="col">Task</th>
              <th scope="col">Status</th>
              <th scope="col">Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(task, index) in tasks" :key="index" :class="{'text-decoration-line-through': task.status === 'Completed'}">
              <th scope="row">{{index + 1}}</th>
              <td>{{task.name}}</td>
              <td style="width: 110px">
                <span 
                  @click="changeStatus(index)" 
                  :class="{
                    'text-danger': task.status === 'To-do',
                    'text-warning': task.status === 'In-progress',
                    'text-success': task.status === 'Completed'
                  }"
                  role="button"
                >
                  {{task.status}}
                </span>
              </td>
              <td class="d-flex justify-content-end flex-wrap">
                <button @click="editTask(index)" class="btn btn-sm btn-info mx-1">Edit</button>
                <button @click="deleteTask(index)" class="btn btn-sm btn-danger mx-1">Delete</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'TodoApp',
  data() {
    return {
      inputValue: '',
      editedTask: null,
      availStatuses: ['To-do', 'In-progress', 'Completed'],
      tasks: [
        {
          name: 'The banana have been stolen from the store.',
          status: 'To-do',
        },
        {
          name: 'The banana have been stolen from the store.',
          status: 'In-progress',
        },
      ]
    }
  },
  methods: {
    submitTask(){
      if(this.inputValue.length === 0) return 
      
      if(this.editedTask === null) {
        this.tasks.push({
          name: this.inputValue,
          status: 'to-do'
        })
      } else {
        this.tasks[this.editedTask].name = this.inputValue;
        this.editedTask = null;
      }

      this.inputValue = '';
    },

    deleteTask(index){
      this.tasks.splice(index, 1);
    },

    editTask(index){
      this.inputValue = this.tasks[index].name;
      this.editedTask = index;
    },

    changeStatus(index) {
      let thisIndex = this.availStatuses.indexOf(this.tasks[index].status);
      if(++thisIndex > 2) thisIndex = 0;
      this.tasks[index].status = this.availStatuses[thisIndex];
    }
  }
}
</script>
<style lang="scss" scoped>
  * {
    font-family: 'Roboto', system-ui, sans-serif;
  }
  .todo-app-container {
    position: relative;
    width: 100%;
    min-height: 100vh;
    background-color: #e7f1f3;
    padding: 5vw;
    
    .container {
      max-width: 650px;
      margin-inline: auto;
    }
  }
</style>
