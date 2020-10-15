<template>
    <section class="main">

      <ul class="todoList">
        <li>
          <div class="view">
            <input class="toggle" type="checkbox"/>
            <label>
              SSS
            </label>
            <button class="destroy"/>
          </div>
        </li>
      </ul>
      <filter-section-view />
    </section>
</template>

<script>
import filterSectionView from "./filter-section-view";

export default {
  data(){
    return {

    }
  },
  components: {
    "filter-section-view": filterSectionView
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

.todoList li {
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
@media screen and (-webkit-min-device-pixel-ratio:0) {
/*// .toggleAll,*/
/*// .todoList li .toggle {*/
/*   //   background: none;*/
/*   // }*/

  .todoList li .toggle {
    height: 20px;
  }

  .todoList li .toggle:checked  {
    height: 40px !important;
  }

  .toggleAll {
    transform: rotate(90deg);
    -webkit-appearance: none;
    -moz-appearance: none;
  }
}
</style>