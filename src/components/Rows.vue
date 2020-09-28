<template>
  <div>
    <div v-bind:key="status.row" v-for="status in statuses">
      <div class="row_container">
        <div class="row_item">
          {{ status.row }}
        </div>
        <div class="row_item">
          <span v-if="status.status == 'Error'" class="warning_icon">!</span>
          <span v-else class="danger_icon">X</span> {{ status.status }}
        </div>
        <div class="row_item">
          {{ status.reason }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Rows",
  props: ["statuses"],
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.row_container div {
  display: inline-block;
  min-width: 27%;
  padding: 7px 3%;
  text-align: left;
}

/* taks: warning and danger icon instead of picture. 
purpose: We're not supplying icons for the arrows, the red X or the yellow exclamation point, so feel free to implement these however you'd like. Your icons don't need to look like our icons. */
.danger_icon {
  background: #d7432d;
  color: white;
  padding: 5px 8px 4px;
  font-size: 12px;
  font-weight: 900;
  border-radius: 50%;
}
.warning_icon {
  background: #f7ca03;
  color: white;
  padding: 5px 10px 4px;
  font-size: 12px;
  font-weight: 900;
  border-radius: 50%;
}
</style>
