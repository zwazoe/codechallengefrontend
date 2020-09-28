<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div class="table_container">
      <div class="header_container">
        <div class="header_item" v-on:click="() => onSort('row')">
          Row
          <i class=" asc  " v-bind:class="() => headerIconClass('row')"></i>
        </div>
        <div class="header_item" v-on:click="() => onSort('status')">
          Status
          <i class=" asc  " v-bind:class="() => headerIconClass('status')"></i>
        </div>
        <div class="header_item" v-on:click="() => onSort('reason')">
          Reason
          <i class=" asc  " v-bind:class="() => headerIconClass('reason')"></i>
        </div>
      </div>
      <div>
        <Rows v-bind:statuses="statuses" />
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Rows from "./Rows";

export default {
  name: "Table",
  props: {
    msg: String,
  },
  methods: {
    onSort(section) {
      let toggle_direction = this.sort[1] == "asc" ? "desc" : "asc";
      this.sort = [section, toggle_direction];
      axios
        .get(
          `http://localhost:5000/status?show=error,failure&&sort=${this.sort[0]},${this.sort[1]}`
        )
        .then((res) => (this.statuses = res.data));
    },
  },
  components: {
    Rows,
  },
  mounted() {
    axios
      .get(
        `http://localhost:5000/status?show=error,failure&&sort=${this.sort[0]},${this.sort[0]}`
      )
      .then((res) => (this.statuses = res.data));
  },
  data: () => {
    return { statuses: [{ item: "text" }], sort: ["row", "asc"] };
  },
  computed: {
    headerIconClass: function(field) {
      return {
        asc: this.sort[1] == "asc",
        desc: this.sort[1] !== "asc",
        arrow_active: this.sort[0] == field,
        arrow_inactive: this.sort[0] !== field,
      };
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.header_container div,
.row_container div {
  display: inline-block;
  min-width: 27%;
  padding: 20px 3%;
  text-align: left;
}
.header_container {
  background-color: #f5f7f9;
}
.table_container {
  width: 800px;
  border: 1px solid #eef1f3;
  border-radius: 3px;
}

.asc {
  transform: rotate(45deg);
  -webkit-transform: rotate(45deg);
}
.desc {
  transform: rotate(-135deg);
  -webkit-transform: rotate(-135deg);
}

.header_item {
  cursor: pointer;
}

.header_item > i {
  margin-left: 5px;
}

.arrow_active {
  border: solid #000;
  border-width: 0 3px 3px 0;
  display: inline-block;
  padding: 3px;
}
.arrow_inactive {
  border: solid #999;
  border-width: 0 3px 3px 0;
  display: inline-block;
  padding: 3px;
}

.danger_icon {
  background: #d7432d;
  color: white;
  padding: 3px 6px 2px;
  font-size: 10px;
  font-weight: bolder;
  border-radius: 50%;
}
.warning_icon {
  background: #f7ca03;
  color: white;
  padding: 3px 9px;
  font-size: 10px;
  font-weight: bolder;
  border-radius: 50%;
}
</style>

<!-- 
Style: 

1. I love underscore simply because I can double click to copy. Also if needs to be, I can use the key as a title. 
For example: key: hello_world
I can do key.replace("_", " ")

-->
