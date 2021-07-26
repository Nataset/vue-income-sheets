<template>
  <div id="app">
    <line-chart class="container" :chartData="datacollection"></line-chart>
    <div
      class="
        container
        mt-3
        border border-1 border-secondary
        rounded-3
        p-3
        ps-5
        pe-5
      "
    >
      <div class="row align-items-center">
        <div class="col pe-5">
          <label class="form-label"
            >Start : {{ month[chartMonthStart - 1] }}</label
          >
          <input
            type="range"
            class="form-range"
            min="1"
            v-model="chartMonthStart"
            v-bind:max="chartMonthEnd"
            step="1"
          />
        </div>
        <div class="col ps-5">
          <label class="form-label">End : {{ month[chartMonthEnd - 1] }}</label>
          <input
            type="range"
            class="form-range"
            v-bind:min="chartMonthStart"
            v-model="chartMonthEnd"
            max="12"
            step="1"
          />
        </div>
        <div class="col ps-5 align-self-center">
          <label>Select Year :&nbsp;&nbsp;&nbsp;&nbsp;</label>
          <div class="dropdown d-inline">
            <button
              class="btn btn-outline-primary dropdown-toggle"
              type="button"
              data-bs-toggle="dropdown"
            >
              {{ chartYear }}
            </button>
            <ul class="dropdown-menu">
              <li v-for="(year, index) in allChartYear" v-bind:key="index">
                <button class="dropdown-item" v-on:click="chartYear = year">
                  {{ year }}
                </button>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
    <div class="container mt-5">
      <div class="row mx-auto align-items-center">
        <iframe
          width="560"
          height="315"
          src="https://www.youtube.com/embed/dQw4w9WgXcQ?autoplay=1&mute=1&enablejsapi=1"
          class="col"
          title="YouTube video player"
          frameborder="0"
          allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
          allowfullscreen
        ></iframe>
        <div class="col">
          <div class="">
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
            <div style="text-align: center">
              <label style="font-size: 14px; font-weight: bold">
                Balance: {{ totalIncome > totalExpense ? "+" : "-"
                }}{{ totalIncome - totalExpense }} |
                <label style="font-weight: normal"
                  >Expense:
                  <label style="color: red">{{ totalExpense }}</label>
                </label>
                <label style="font-weight: normal"
                  >, Income:
                  <label style="color: green">{{ totalIncome }}</label>
                </label>
              </label>
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
      totalIncome: 0,
      totalExpense: 0,
      chartIncome: 0,
      chartExpense: 0,
      chartYear: 2021,
      allChartYear: new Set(),
      chartMonthStart: 1,
      chartMonthEnd: 12,
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
      month: [
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
    };
  },
  components: { DatePicker, LineChart },
  created: function () {
    let self = this;
    setInterval(function () {
      self.timeNow = moment().format("dddd, MMMM Do YYYY, h:mm:ss a");
    }, 1000);
  },
  watch: {
    chartMonthStart: function () {
      this.updateChart();
    },
    chartMonthEnd: function () {
      this.updateChart();
    },
    chartYear: function () {
      this.updateChart();
    },
  },
  methods: {
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
      const newPushData = JSON.parse(JSON.stringify(this.newData));
      this.info.push(newPushData);
      console.log(this.newData);
    },

    setChartData() {
      this.chartIncome = 0;
      this.chartExpense = 0;
      this.chartData.expense = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];
      this.chartData.income = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];

      this.info.forEach((el) => {
        const time = moment(el.date);
        const month = time.format("M");
        const year = time.format("YYYY");
        this.allChartYear.add(year);

        if (year == this.chartYear) {
          if (el.type === "income") {
            this.chartData.income[parseInt(month) - 1] += parseInt(el.amount);
            this.chartIncome += parseInt(el.amount);
          } else {
            this.chartData.expense[parseInt(month) - 1] += parseInt(el.amount);
            this.chartExpense += parseInt(el.amount);
          }
        }
      });
    },

    updateChart() {
      this.setChartData();
      this.datacollection = {
        labels: this.month.slice(this.chartMonthStart - 1, this.chartMonthEnd),
        datasets: [
          {
            label:
              "Total Income " +
              this.chartYear +
              ": " +
              this.chartIncome +
              " baht.",
            borderColor: "#05CBE1",
            pointBackgroundColor: "green",
            pointBorderColor: "green",
            borderWidth: 1,
            backgroundColor: this.gradient2,
            data: this.chartData.income,
          },
          {
            label:
              "Total Expense " +
              this.chartYear +
              ": " +
              this.chartExpense +
              " baht.",
            borderColor: "#FC2525",
            pointBackgroundColor: "red",
            borderWidth: 1,
            pointBorderColor: "red",
            backgroundColor: this.gradient,
            data: this.chartData.expense,
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

    findTotal() {
      this.info.forEach((el) => {
        el.type === "income"
          ? (this.totalIncome += parseInt(el.amount))
          : (this.totalExpense += parseInt(el.amount));
      });
    },

    updateData() {
      this.info = this.sortDate(this.info);
      this.findTotal();
      this.updateChart();
      this.cancle();
    },
  },

  mounted() {
    this.getChartColor();
    fetch("data.json")
      .then((res) => res.json())
      .then((data) => {
        this.info = this.sortDate(data);
        this.findTotal();
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
