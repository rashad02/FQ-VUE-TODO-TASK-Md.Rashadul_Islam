<template>
  <section class="main">
    <text-input-section @customEvent="elementEvent"/>
    <ul class="todoList" v-if="todoList.length> 0">
      <li class="todo-item" v-for="todoItem in todoList" :key="todoItem.id">
        <div class="view">
          <template v-if="isEditing && editingTodoId === todoItem.id">
            <text-input-section :todoItem="todoItem" @customEvent="elementEvent"/>
          </template>
          <template v-else>
            <input class="toggle" :checked="todoItem.isCompleted" @change="changeTaskStatus(todoItem.id)"
                   type="checkbox"/>
            <label @dblclick="editTodo(todoItem.id)">
              {{ todoItem.todo }}
            </label>
            <button class="destroy" @click="removeTodo(todoItem.id)"/>
          </template>


        </div>
      </li>
    </ul>
    <filter-section-view :key="lastTodoId" @customEvent="elementEvent"/>
  </section>
</template>

<script>
import crypto from "crypto";
import _ from 'lodash';
import textInputSection from './text-input-section';
import filterSectionView from "./filter-section-view";

export default {
  data() {
    return {
      todoList: localStorage.getItem("todo", this) || [],
      lastTodoId: 0,
      isCompleted: false,
      isEditing: false,
      editingTodoId: "",
    }
  },
  components: {
    "text-input-section": textInputSection,
    "filter-section-view": filterSectionView
  },
  methods: {
    changeTaskStatus: function (id) {

      let instance = this,
          todoList = [];

      _.each(instance.todoList, todo => {
        if (todo.id === id) {
          todo.isCompleted = !todo.isCompleted;
        }
        todoList.push(todo);
      });

      localStorage.setItem("todo", JSON.stringify(todoList));
      instance.todoList = todoList;
    },
    editTodo: function (todoId) {
      this.isEditing = true;
      this.editingTodoId = todoId;
    },
    removeTodo: function (id) {
      let instance = this,
          todoList = instance.todoList;

      instance.todoList = _.filter(todoList, todo => {
        return todo.id !== id;
      });

      localStorage.setItem("todo", JSON.stringify(instance.todoList));

      instance.todoListCount = _.size(instance.todoList);

      instance.lastTodoId = crypto.randomBytes(16).toString("hex");

    },
    elementEvent: function (data) {
      let filter = data.filter || "",
          currentTodoList = JSON.parse(localStorage.getItem('todo')) || [];

      if (data.newId) this.lastTodoId = data.newId;

      if (data.type === "item_updated" || data.type === "redo_todo") {
        this.isEditing = false;
        this.editingTodoId = "";
      }

      if (data.type === "filter_updated" && filter !== "SHOW_ALL") {
        if (filter === "SHOW_COMPLETED") {
          this.todoList = _.filter(currentTodoList, todo => todo.isCompleted);

        } else {
          this.todoList = _.filter(currentTodoList, todo => !todo.isCompleted);
        }
      } else {
        this.todoList = currentTodoList;
      }

    }
  },
  mounted() {

    this.$nextTick(function () {
      this.todoList = JSON.parse(localStorage.getItem('todo')) || [];
      this.todoListCount = _.size(this.todoList) || 0;
    })

  }
}
</script>

<style scoped>
.main {
  position: relative;
  z-index: 2;
  border-top: 1px solid #e6e6e6;
}

.todoList {
  margin: 0;
  padding: 0;
  list-style: none;
}

.todo-item {
  position: relative;
  font-size: 24px;
  border-bottom: 1px solid #ededed;
}

.todoList li:last-child {
  border-bottom: none;
}

.todoList li.editing {
  border-bottom: none;
  padding: 0;
}

.todoList li.editing .edit {
  display: block;
  width: 506px;
  padding: 13px 17px 12px 17px;
  margin: 0 0 0 43px;
}

.todoList li.editing .view {
  display: none;
}

.toggle {
  text-align: center;
  width: 40px;
  /* auto, since non-WebKit browsers doesn't support input styling */
  height: auto;
  position: absolute;
  top: 0 !important;
  bottom: 0;
  margin: auto 0;
  border: none; /* Mobile Safari */
  -webkit-appearance: none;
  -moz-appearance: none;
}

.todoList li .toggle {
  text-align: center;
  width: 40px;
  /* auto, since non-WebKit browsers doesn't support input styling */
  height: auto;
  position: absolute;
  bottom: 0;
  margin: auto 0;
  border: none; /* Mobile Safari */
  color: white;
  -webkit-appearance: none !important;
  -moz-appearance: none !important;
  appearance: none !important;
}

.todoList li .toggle:focus {
  outline: none;
}


.todoList li label {
  white-space: pre-line;
  word-break: break-all;
  padding: 15px 60px 15px 15px;
  margin-left: 45px;
  display: block;
  line-height: 1.2;
  transition: color 0.4s;
}

.todoList li.completed label {
  color: #d9d9d9;
  text-decoration: line-through;
}

.todoList li .destroy {
  display: none;
  position: absolute;
  top: 0;
  right: 10px;
  bottom: 0;
  width: 40px;
  height: 40px;
  margin: auto 0;
  font-size: 30px;
  color: #cc9a9a;
  margin-bottom: 11px;
  transition: color 0.2s ease-out;
}

.todoList li .destroy:hover {
  color: #af5b5e;
}

.todoList li .destroy:after {
  content: '×';
}

.todoList li:hover .destroy {
  display: block;
}

.todoList li .edit {
  display: none;
}

.todoList li.editing:last-child {
  margin-bottom: -1px;
}

.toggle:after {
  content: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path fill="none" d="M0 0h24v24H0V0z"/><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.42 0-8-3.58-8-8s3.58-8 8-8 8 3.58 8 8-3.58 8-8 8z"/></svg>');
}

.toggle:checked:after {
  content: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path d="M12 0c-6.627 0-12 5.373-12 12s5.373 12 12 12 12-5.373 12-12-5.373-12-12-12zm-1.25 16.518l-4.5-4.319 1.396-1.435 3.078 2.937 6.105-6.218 1.421 1.409-7.5 7.626z"/></svg>');
}

.toggleAll {
  position: absolute;
  top: -55px;
  left: -12px;
  width: 60px;
  height: 34px;
  text-align: center;
  border: none; /* Mobile Safari */
}

.toggleAll:before {
  content: '❯';
  font-size: 22px;
  color: #e6e6e6;
  padding: 10px 27px 10px 27px;
}

.toggleAll:checked:before {
  color: #737373;
}

/*
  Hack to remove background from Mobile Safari.
  Can't use it globally since it destroys checkboxes in Firefox
*/
@media screen and (-webkit-min-device-pixel-ratio: 0) {
  .toggleAll,
  .todoList li .toggle {
    background: none;
  }

  .todoList li .toggle {
    height: 20px;
  }

  .toggleAll {
    transform: rotate(90deg);
    -webkit-appearance: none;
    -moz-appearance: none;
  }
}
</style>