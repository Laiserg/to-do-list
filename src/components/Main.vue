<template>
  <div class="main">

    <header>
      <h1>To Do List</h1>
      <form v-on:submit.prevent="addTask">
        <input
                type="text"
                placeholder="Enter a task..."
                v-model="newTaskTitle"
                autofocus
                autocomplete="off"
                class="tast-input"
        >
      </form>
    </header>

    <transition-group
            name="fade"
            enter-active-class="animated"
            leave-active-class="animated"
    >
      <div class="task-list" v-for="(task, index) in filteredTasks" :key="task.id">

        <div class="task-list__item">
          <input class="checkbox" type="checkbox" v-model="task.completed">
          <div
                  class="task-list__item-label"
                  v-if="!task.editing"
                  v-on:dblclick="edit(task)"
                  v-bind:class="{ 'task-list__item-completed' : task.completed }"
          >
            {{ task.title }}
          </div>
          <input
                  class="task-list__item-edit"
                  type="text"
                  v-else
                  v-focus
                  v-model="task.title"
                  v-on:blur="doneEdit(task)"
                  v-on:keyup.enter="doneEdit(task)"
                  v-on:keyup.esc="cancelEdit(task)"
          >
        </div>

        <div v-on:click="removeTask(index)" class="task-list__remove-item">
          &times;
        </div>

      </div>
    </transition-group>

    <div class="extra-container">
      <div>
        <label>
          <input
                  type="checkbox"
                  v-bind:checked="!anyRemaining"
                  v-on:change="checkAllTask"
          > Check all as completed
        </label>
      </div>
      <div>
        {{ remaining }} tasks left
      </div>
    </div>

    <div class="extra-container">
      <div>
        <span>Sort tasks: </span>
        <button
                v-bind:class="{ active: filter === 'all'}"
                v-on:click="filter = 'all'"
        >All</button>
        <button
                v-bind:class="{ active: filter === 'active'}"
                v-on:click="filter = 'active'"
        >Not completed</button>
        <button
                v-bind:class="{ active: filter === 'completed'}"
                v-on:click="filter = 'completed'"
        >Completed</button>
      </div>

      <div>
        <transition name="fade">
          <button
                  v-if="showClearCompleted"
                  v-on:click="clearCompleted"
          >Clear completed
          </button>
        </transition>
      </div>
    </div>


  </div>
</template>

<script>

    export default {
        name: 'Main',
        data () {
            return {
                newTaskTitle: '',
                taskId: 4,
                titleBeforeEdit: '',
                filter: 'all',
                tasks: [
                    {
                        'id': 1,
                        'title': 'Task example 1',
                        'completed': false,
                        'editing': false,
                    },
                    {
                        'id': 2,
                        'title': 'Task example 2',
                        'completed': false,
                        'editing': false,
                    },
                    {
                        'id': 3,
                        'title': 'Task example 3',
                        'completed': false,
                        'editing': false,
                    },
                ],
            }
        },
        computed: {
            remaining() {
                return this.tasks.filter(task => !task.completed).length;
            },
            anyRemaining() {
                return this.remaining !== 0;
            },
            filteredTasks() {
                if (this.filter === 'all') {
                    return this.tasks;
                } else if (this.filter === 'active') {
                    return this.tasks.filter(task => !task.completed);
                } else if (this.filter === 'completed') {
                    return this.tasks.filter(task => task.completed);
                }

                return this.tasks;
            },
            showClearCompleted() {
                return this.tasks.filter(task => task.completed).length > 0;
            }
        },
        directives: {
            focus: {
                inserted: function (e) {
                    e.focus();
                }
            }
        },
        methods: {
            addTask() {
                if (this.newTaskTitle.trim().length !== 0) {

                    this.tasks.push({
                        id: this.taskId,
                        title: this.newTaskTitle,
                        completed: false
                    });

                    this.newTaskTitle = '';
                    this.taskId++;

                }
            },
            removeTask(index) {
                this.tasks.splice(index, 1);
            },
            edit(task) {
                this.titleBeforeEdit = task.title;
                task.editing = true;
            },
            doneEdit(task) {
                if (task.title.trim().length === 0) {
                    task.title = this.titleBeforeEdit;
                }
                task.editing = false;
            },
            cancelEdit(task) {
                task.title = this.titleBeforeEdit;
                task.editing = false;
            },
            checkAllTask() {
                this.tasks.forEach((task) => task.completed = event.target.checked);
            },
            clearCompleted() {
                this.tasks = this.tasks.filter(task => !task.completed);
            }
        }
    }
</script>

<style scoped>
  @import "https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css";

  .main {
    padding: 20px 0 0 0;
  }

  h1 {
    text-align: center;
  }

  .tast-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
  }

  .tast-input:focus {
    outline: none;
  }

  .task-list {
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: .3s;
    transition: all 0.3s ease-out;
    height: 70px;
  }

  .task-list__item {
    display: flex;
    align-items: center;
  }

  .task-list__item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }

  .task-list__remove-item {
    cursor: pointer;
    transition: all 0.2s ease-out;
    margin-left: 14px;
    font-size: 30px;
  }

  .task-list__remove-item:hover {
    color: #d42017;
  }

  .task-list__item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
  }

  .task-list__item-edit:focus {
     outline: none;
   }

  .task-list__item-completed {
    text-decoration: line-through;
    color: grey;
  }

  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
  }

  button {
    border: 1px solid #d4d4d4;
    color: #616161;
    border-radius: 3px;
    cursor: pointer;
    padding: 3px 10px;
    margin: 0 2px;
    font-size: 14px;
    background-color: white;
  }

  button:hover {
    background: #7feb80;
  }

  button:focus {
    outline: none;
  }

  .active {
    background: #16c53d;
    border: 1px solid #1a901a;
    color: black;
  }

  .fade-enter-active, .fade-leave-active {
    transition: all .2s ease-out;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
  }

  .fade-enter {
    height: 0;
  }

  .fade-leave-to {
    height: 0;
  }

</style>
