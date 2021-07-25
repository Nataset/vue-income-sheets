<template>
  <div id="app">
    <canvas style="display: none" ref="test"></canvas>
    <line-chart class="container" :chartData="datacollection"></line-chart>
    <div class="container">
      <div class="row mx-auto">
        <iframe
          width="560"
          height="315"
          src="https://www.youtube.com/embed/dQw4w9WgXcQ?autoplay=1&mute=1&enablejsapi=1"
          class="col mt-5"
          title="YouTube video player"
          frameborder="0"
          allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
          allowfullscreen
        ></iframe>
        <div class="col">
          <div class="mt-5">
            Current Time : {{ timeNow ? timeNow : "Loading... Current Time" }}
          </div>
          <div
            class="container border border-1 border-secondary rounded-3 mt-1"
            id="IncomeFrom"
          >
            <span>Select Type :</span>
            <div class="dropdown d-inline">
              <button
                class="btn btn-outline-primary dropdown-toggle"
                type="button"
                data-bs-toggle="dropdown"
              >
                {{ isIncome ? "Income" : "Expenses" }}
              </button>
              <ul class="dropdown-menu">
                <li>
                  <button
                    class="dropdown-item"
                    v-on:click="isIncome = !isIncome"
                  >
                    {{ isIncome ? "Expenses" : "Income" }}
                  </button>
                </li>
              </ul>
            </div>
            <div>
              <input
                type="text"
                class="form-control"
                v-model="newData.amount"
                v-bind:placeholder="[
                  isIncome
                    ? 'Enter amount of income'
                    : 'Enter amount of expenses',
                ]"
              />
            </div>
            <div>
              <input
                type="text"
                class="form-control"
                v-model="newData.describe"
                placeholder="Enter Description"
              />
            </div>
            <div>
              <span>Select date : </span>
              <date-picker v-model="newData.date" class="ms-3"></date-picker>
            </div>
            <div>
              <button
                type="button"
                class="btn btn-outline-success w-50"
                v-on:click="add"
              >
                Add
              </button>
              <button
                type="button"
                class="btn btn-outline-danger w-50"
                @click="cancle"
              >
                Cancel
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="container mt-5 mb-5">
      <table
        class="
          table table-striped table-bordered table-hover
          border border-1 border
        "
      >
        <thead class="table-dark">
          <tr>
            <th style="width: 15%" scope="col">Type</th>
            <th style="width: 15%" scope="col">Date</th>
            <th style="width: 50%" scope="col">Description</th>
            <th style="width: 20%" scope="col">Amount</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(items, index) in info" v-bind:key="index">
            <td
              v-bind:class="[
                items.type === 'income' ? 'text-success' : 'text-danger',
              ]"
            >
              {{ items.type.capitalize() }}
            </td>
            <td>{{ items.date }}</td>
            <td id="description">
              &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{{ items.describe }} #{{
                index
              }}
            </td>
            <td
              v-bind:class="[
                items.type === 'income' ? 'text-success' : 'text-danger',
              ]"
            >
              {{ items.type === "income" ? "+" : "-" }} {{ items.amount }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import moment from "moment";
import DatePicker from "vue2-datepicker";
import LineChart from "./LineChart.vue";
import "vue2-datepicker/index.css";

String.prototype.capitalize = function () {
  return this.charAt(0).toUpperCase() + this.slice(1);
};

export default {
  data() {
    return {
      info: null,
      timeNow: null,
      isIncome: null,
      newData: {
        type: "",
        date: "",
        describe: "",
        amount: "",
      },
      chartData: {
        income: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
        expense: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
      },
      gradient: null,
      gradient2: null,
      datacollection: null,
    };
  },
  created: function () {
    let self = this;
    setInterval(function () {
      self.timeNow = moment().format("dddd, MMMM Do YYYY, h:mm:ss a");
    }, 1000);
  },
  components: { DatePicker, LineChart },
  methods: {
    moment() {
      return moment();
    },
    sortDate(date) {
      const sortDate = date.sort((a, b) => {
        return moment.utc(b.date).diff(moment.utc(a.date));
      });
      return sortDate;
    },
    cancle() {
      this.newData = {
        type: "",
        date: "",
        describe: "",
        amount: "",
      };
    },

    add() {
      this.newData.type = this.isIncome ? "income" : "expense";
      this.newData.date = moment(this.newData.date).format("YYYY-MM-DD");
      const newPushData = this.newData;
      this.info.push(newPushData);
      this.setChartData();
      this.updateChart();
      this.cancle();
    },

    setChartData() {
      this.info.forEach((el) => {
        const time = moment(el.date);
        const month = time.format("M");
        const year = time.format("YYYY");
        console.log(parseInt(month), parseInt(el.amount));
        if (year == 2021) {
          el.type === "income"
            ? (this.chartData.income[parseInt(month) - 1] += parseInt(
                el.amount
              ))
            : (this.chartData.expense[parseInt(month) - 1] += parseInt(
                el.amount
              ));
        }
      });
    },

    updateChart() {
      console.log();
      this.datacollection = {
        labels: [
          "January",
          "February",
          "March",
          "April",
          "May",
          "June",
          "July",
          "August",
          "September",
          "October",
          "November",
          "December",
        ],
        datasets: [
          {
            label: "Expense",
            borderColor: "#FC2525",
            pointBackgroundColor: "red",
            borderWidth: 1,
            pointBorderColor: "red",
            backgroundColor: this.gradient,
            data: this.chartData.expense,
          },
          {
            label: "Income",
            borderColor: "#05CBE1",
            pointBackgroundColor: "green",
            pointBorderColor: "green",
            borderWidth: 1,
            backgroundColor: this.gradient2,
            data: this.chartData.income,
          },
        ],
      };
    },

    getChartColor() {
      this.gradient = document
        .createElement("canvas")
        .getContext("2d")
        .createLinearGradient(0, 0, 0, 450);
      this.gradient2 = document
        .createElement("canvas")
        .getContext("2d")
        .createLinearGradient(0, 0, 0, 450);

      this.gradient.addColorStop(0, "rgba(255, 0,0, 0.5)");
      this.gradient.addColorStop(0.5, "rgba(255, 0, 0, 0.25)");
      this.gradient.addColorStop(1, "rgba(255, 0, 0, 0)");

      this.gradient2.addColorStop(0, "rgba(0, 255, 0, 0.9)");
      this.gradient2.addColorStop(0.5, "rgba(0, 255, 0, 0.25)");
      this.gradient2.addColorStop(1, "rgba(0, 255, 0, 0)");
    },
  },

  mounted() {
    this.getChartColor();
    fetch("data.json")
      .then((res) => res.json())
      .then((data) => {
        this.info = this.sortDate(data);
        this.setChartData();
        this.updateChart();
      });
  },
};
</script>

<style>
* {
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 30px;
}

#IncomeFrom {
  text-align: left;
  padding: 10px;
  width: 370px;
}

#IncomeFrom > * {
  padding: 10px;
}

#description {
  text-align: left;
}

.small {
  max-width: 600px;
  margin: 150px auto;
}
</style>
