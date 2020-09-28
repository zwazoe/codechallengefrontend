<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div class="table_container">
      <div class="header_container">
        <!-- task: show h title and sort values based on such column.
        purpose: Clicking on a column header that is inactive (i.e. clicking on "Status" when "Row" is active) should cause the clicked method to activate.

         -->
        <div class="header_item" v-on:click="() => onSort('row')">
          Row
          <i class="arrow_active" v-bind:class="[dirClass, rowClass]"></i>
        </div>
        <div class="header_item" v-on:click="() => onSort('status')">
          Status
          <i class="arrow_active" v-bind:class="[dirClass, statusClass]"></i>
        </div>
        <div class="header_item" v-on:click="() => onSort('reason')">
          Reason
          <i class="arrow_active" v-bind:class="[dirClass, reasonClass]"></i>
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
// task: import the data via client side in case server is on running.
// purpose: Attached to this email is a JSON data file for your code to consume.
// purpose:The array provided in the JSON file will have a number of Successes, Errors and Failures.
import data from "../data/data.json";
import { getIncludes, defaultValue, sortValue } from "../mw/search";

export default {
  name: "Table",
  props: {
    msg: String,
  },
  methods: {
    // task: serve data, purpose: backup server
    clientSideServe() {
      // get the keys that should be displayed and make an array. If no key, default to such array
      // purpose: For this table, we only want to see Errors and Failures.
      let status = this.show ? this.show.split(",") : ["error", "failure"];
      // if nomenclature is absent, default based to these values
      let default_dict = {
        status: "Error",
        reason: "Server Error",
      };
      // get statuses via these callback functions.
      this.statuses = getIncludes(data, status, (v) =>
        defaultValue(v, default_dict, (v) => sortValue(v, this.sort))
      );
    },
    // task: sort data,
    //purpose: Only 1 sorting method should ever be active at a time.

    onSort(section) {
      //purpse: Clicking on a column header that is already active (i.e. clicking on "Row" when "Row" is active) should toggle the direction of the arrow and the sorted data.
      let toggle_direction = this.sort[1] === "asc" ? "desc" : "asc";

      this.sort = [section, toggle_direction];

      // if server is available, data will be sorted and sanitized by the server.
      // if not, data will be sorted and sanitized by client.
      // purpose: fetch new data based on new sort criteriea

      axios
        .get(
          `http://localhost:5000/status?show=error,failure&&sort=${this.sort[0]},${this.sort[1]}`
        )
        .then((res) => (this.statuses = res.data))
        .catch(() => {
          this.clientSideServe();
        });
    },
  },
  components: {
    Rows,
  },
  mounted() {
    //task: if server is available, data will be sorted and sanitized by the server.
    // if not, data will be sorted and sanitized by client.
    // purpose: show of knowledge
    axios
      .get(
        `http://localhost:5000/status?show=${this.show}&&sort=${this.sort[0]},${this.sort[1]}`
      )
      .then((res) => (this.statuses = res.data))
      .catch(() => {
        this.clientSideServe();
      });
  },
  data: () => {
    return {
      statuses: [],
      //task: On initial load, your table should default to the "Row" sort,
      // purpose: Arrows should default to pointing down
      sort: ["row", "asc"],
      show: "error,failure",
    };
  },
  computed: {
    // task: set the row class, depending on the sort value.
    // purpose: Clicking on a column header that is inactive (i.e. clicking on "Status" when "Row" is active) should cause the clicked method to activate.

    rowClass() {
      return {
        // task: assign class to get icon.
        // purpose: Clicking on a column header that is already active (i.e. clicking on "Row" when "Row" is active) should toggle the direction of the arrow and the sorted data.
        asc: this.sort[0] === "row" && this.sort[1] === "desc",
        // i prefer commented out code because it provide neutriality
        // desc: this.sort[0] === "row" && this.sort[1] === "asc",
        desc: this.sort[0] !== "row" || this.sort[1] !== "desc",
        // assign class to display icon
        // pupose: When a particular sorting method is active, the arrow icon associated with that sort should darken. The other arrows should turn gray. In the example image above, the "Row" sort is active, so the arrow next to the "Row" text is black.
        arrow_active: this.sort[0] === "row",
        arrow_inactive: this.sort[0] !== "row",
      };
    },
    statusClass() {
      return {
        asc: this.sort[0] === "status" && this.sort[1] === "desc",
        // i prefer commented out code because it provide neutriality
        // desc: this.sort[0] === "status" && this.sort[1] === "asc",
        desc: this.sort[0] !== "status" || this.sort[1] !== "desc",
        arrow_active: this.sort[0] === "status",
        arrow_inactive: this.sort[0] !== "status",
      };
    },
    reasonClass() {
      return {
        asc: this.sort[0] === "reason" && this.sort[1] === "desc",
        // i prefer commented out code because it provide neutriality
        // desc: this.sort[0] === "reason" && this.sort[1] === "asc",
        desc: this.sort[0] !== "reason" || this.sort[1] !== "desc",

        arrow_active: this.sort[0] === "reason",
        arrow_inactive: this.sort[0] !== "reason",
      };
    },
  },
};
</script>

<style scoped>
.header_container div {
  display: inline-block;
  min-width: 27%;
  padding: 10px 3%;
  text-align: left;
}
.header_container {
  background-color: #f5f7f9;
}
.table_container {
  width: 600px;
  border: 1px solid #eef1f3;
  border-radius: 3px;
}

.header_item {
  cursor: pointer;
}

.header_item > i {
  margin-left: 5px;
}
/* task : create arrow icons:
purpose: Arrows should default to pointing down.
Note: on sorting tables, arrows should show neutral when not in used. 
either hide the arrow. Thefore, if not active, arrow will be pointed 
down on a neutral manner. 

 */
.asc {
  transform: rotate(-135deg);
  -webkit-transform: rotate(-135deg);
}
.desc {
  transform: rotate(45deg);
  -webkit-transform: rotate(45deg);
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
