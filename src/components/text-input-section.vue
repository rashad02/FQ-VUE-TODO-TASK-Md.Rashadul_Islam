<template>
  <div>
    <input id="" class="new" type="text" placeholder="What to do next!" @change="updateTodo($event)"/>
  </div>
</template>

<script>
export default {
  data(){
    return {

    }
  },
  methods: {
    updateTodo: (e) => {
        console.log("e", e.target.value);
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
  box-shadow: inset 0 -2px 1px rgba(0,0,0,0.03);
}
</style>