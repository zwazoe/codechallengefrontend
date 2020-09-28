<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div class="table_container">
      <div class="header_container">
        <div class="header_item" v-on:click="() => onSort('row')">
          Row
          <i class="  arrow_active " v-bind:class="[dirClass, rowClass]"></i>
        </div>
        <div class="header_item" v-on:click="() => onSort('status')">
          Status
          <i class="  arrow_active " v-bind:class="[dirClass, statusClass]"></i>
        </div>
        <div class="header_item" v-on:click="() => onSort('reason')">
          Reason
          <i class="  arrow_active " v-bind:class="[dirClass, reasonClass]"></i>
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
        `http://localhost:5000/status?show=error,failure&&sort=${this.sort[0]},${this.sort[1]}`
      )
      .then((res) => (this.statuses = res.data));
  },
  data: () => {
    return { statuses: [{ item: "text" }], sort: ["row", "asc"] };
  },
  computed: {
    rowClass() {
      return {
        asc: this.sort[0] == "row" && this.sort[1] !== "asc",
        desc: this.sort[0] == "row" && this.sort[1] == "asc",
        arrow_active: this.sort[0] == "row",
        arrow_inactive: this.sort[0] !== "row",
      };
    },
    statusClass() {
      return {
        asc: this.sort[0] == "status" && this.sort[1] !== "asc",
        desc: this.sort[0] == "status" && this.sort[1] == "asc",
        arrow_active: this.sort[0] == "status",
        arrow_inactive: this.sort[0] !== "status",
      };
    },
    reasonClass() {
      return {
        asc: this.sort[0] == "reason" && this.sort[1] !== "asc",
        desc: this.sort[0] == "reason" && this.sort[1] == "asc",
        arrow_active: this.sort[0] == "reason",
        arrow_inactive: this.sort[0] !== "reason",
      };
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.header_container div {
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
  transform: rotate(-135deg);
  -webkit-transform: rotate(-135deg);
}
.desc {
  transform: rotate(45deg);
  -webkit-transform: rotate(45deg);
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
</style>
