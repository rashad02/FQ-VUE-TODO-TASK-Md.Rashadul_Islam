<template>
  <div>
    <input id="" :class="{'edit': todoItem, 'new': !todoItem }" type="text" :value="todoName"
           placeholder="What to do next!"
           @blur="redoTodo()" @change="updateTodoNameValue($event)" @keyup.enter="addTodo()"/>
  </div>
</template>

<script>
import crypto from "crypto";
import _ from "lodash";

export default {
  props: ['todoItem'],
  data() {
    return {
      todoId: this.todoItem && this.todoItem.id ? this.todoItem.id : "",
      todoName: this.todoItem && this.todoItem.todo ? this.todoItem.todo : ""
    }
  },
  methods: {
    updateTodoNameValue: function (e) {
      this.todoName = e.target.value;
    },
    addTodo: function () {

      let instance = this,
          emitData = {type: "new_item_added"},
          todoItem = {
            id: instance.todoId,
            todo: instance.todoName,
          };


      if (todoItem) {
        let todoList = JSON.parse(localStorage.getItem('todo')) || [];

        if (instance.todoId) {

          _.each(todoList, todoListItem => {
            if (todoListItem.id === instance.todoId) {
              todoListItem.todo = todoItem.todo;
            }
          });

          emitData.type = "item_updated";

        } else {
          todoItem.id = crypto.randomBytes(16).toString("hex");
          instance.todoName = "";
          todoList.push(todoItem);
        }

        emitData.newId = todoItem.id;


        localStorage.setItem("todo", JSON.stringify(todoList));
        instance.$emit("customEvent", emitData)

      }
    },
    redoTodo: function () {
      this.$emit("customEvent", {type: "redo_todo", todoId: this.todoId});
    }
  }
}
</script>

<style scoped>
.new,
.edit {
  position: relative;
  margin: 0;
  width: 100%;
  font-size: 24px;
  font-family: inherit;
  font-weight: inherit;
  line-height: 1.4em;
  border: 0;
  outline: none;
  color: inherit;
  padding: 6px;
  border: 1px solid #999;
  box-shadow: inset 0 -1px 5px 0 rgba(0, 0, 0, 0.2);
  box-sizing: border-box;
  /*font-smoothing: antialiased;*/
}

.new {
  padding: 16px 16px 16px 60px;
  border: none;
  background: rgba(0, 0, 0, 0.003);
  box-shadow: inset 0 -2px 1px rgba(0, 0, 0, 0.03);
}
</style>