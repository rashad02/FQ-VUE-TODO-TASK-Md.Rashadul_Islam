<template>
  <footer class="footer">
      <span class="todoCount">
        <template>{{ getTodoListText(todoListCount) }}</template>
      </span>
    <ul class='filters' v-if="filters">
      <li v-for="filter in filters" :key="filter">
        <a :class="{'selected': selectedFilter === filter}" @click="setSelectedFilter(filter)">
          {{ getFilterText(filter) }}
        </a>
      </li>
    </ul>
    <button class="clearCompleted" @click="removeCompletedTask()">
      Clear completed
    </button>
  </footer>
</template>

<script>
import _ from "lodash";

export default {

  data() {
    return {
      todoList: [],
      selectedFilter: "SHOW_ALL",
      todoListCount: 0,
      filters: ["SHOW_ALL", "SHOW_COMPLETED", "SHOW_ACTIVE"],
      getTodoListText: function (count) {
        let todoListCount = count,
            text = "";

        if (todoListCount === 0) {
          text = "No item left"
        } else {
          if (todoListCount > 1) {
            text = `${todoListCount} items left`
          } else {
            text = `${todoListCount} item left`
          }
        }
        return text;
      },
      getFilterText: function (filterName) {
        let filterText = "";
        if (filterName === "SHOW_ALL") filterText = "All";
        if (filterName === "SHOW_COMPLETED") filterText = "Completed";
        if (filterName === "SHOW_ACTIVE") filterText = "Active";

        return filterText;
      }
    }
  },
  methods: {
    removeCompletedTask: function () {
      let instance = this,
          todoList = JSON.parse(localStorage.getItem("todo", this)) || [],
          activeTodoList = [];

      activeTodoList = _.filter(todoList, todo => {
        return !todo.isCompleted;
      });
      console.log("after remove: ", activeTodoList);
      localStorage.setItem("todo", JSON.stringify(activeTodoList));
      this.todoListCount = currentTodoCountByFilter(instance.selectedFilter);
      instance.$emit('customEvent', {type: 'clear_completed'});
    },
    setSelectedFilter: function (filterName) {
      this.selectedFilter = filterName;

      this.todoListCount = currentTodoCountByFilter(filterName);
      this.$emit('customEvent', {type: 'filter_updated', filter: filterName});

    }
  },
  mounted() {

    this.$nextTick(function () {
      this.todoList = JSON.parse(localStorage.getItem('todo')) || [];
      this.todoListCount = currentTodoCountByFilter();
    })

  },

}
const currentTodoCountByFilter = function (filterName) {
  let currentTodoList = JSON.parse(localStorage.getItem('todo')) || [],
      todoCount;

  if (!filterName || filterName === "SHOW_ALL") {
    todoCount = _.size(currentTodoList);
  } else {
    if (filterName === "SHOW_COMPLETED") {
      currentTodoList = _.filter(currentTodoList, todo => todo.isCompleted);
      todoCount = _.size(currentTodoList);
    } else {
      currentTodoList = _.filter(currentTodoList, todo => !todo.isCompleted);
      todoCount = _.size(currentTodoList);
    }
  }

  return todoCount;
}
</script>

<style scoped>
.footer {
  color: #777;
  padding: 10px 15px;
  height: 20px;
  text-align: center;
  border-top: 1px solid #e6e6e6;
}

.footer:before {
  content: '';
  position: absolute;
  right: 0;
  bottom: 0;
  left: 0;
  height: 50px;
  overflow: hidden;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2),
  0 8px 0 -3px #f6f6f6,
  0 9px 1px -3px rgba(0, 0, 0, 0.2),
  0 16px 0 -6px #f6f6f6,
  0 17px 2px -6px rgba(0, 0, 0, 0.2);
}

.filters {
  margin: 0;
  padding: 0;
  list-style: none;
  position: absolute;
  right: 0;
  left: 0;
}

.filters li {
  display: inline;
}

.filters li a {
  color: inherit;
  margin: 3px;
  padding: 3px 7px;
  text-decoration: none;
  border: 1px solid transparent;
  border-radius: 3px;
}

.filters li a.selected,
.filters li a:hover {
  cursor: pointer;
  border-color: rgba(175, 47, 47, 0.1);
}

.filters li a.selected {
  border-color: rgba(175, 47, 47, 0.2);
}

.todoCount {
  float: left;
  text-align: left;
}

.todoCount strong {
  font-weight: 300;
}

.clearCompleted,
html .clearCompleted:active {
  float: right;
  position: relative;
  line-height: 20px;
  text-decoration: none;
  cursor: pointer;
}

@media (max-width: 430px) {
  .footer {
    height: 50px;
  }

  .filters {
    bottom: 10px;
  }
}
</style>