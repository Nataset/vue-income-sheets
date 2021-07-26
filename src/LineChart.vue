<script>
import { Line, mixins } from "vue-chartjs";
const { reactiveProp } = mixins;

export default {
  extends: Line,
  mixins: [reactiveProp],
  props: ["chartData"],
  data() {
    return {
      options: { responsive: true, maintainAspectRatio: false },
    };
  },
  mounted() {
    this.renderChart(
      // {
      //   labels: [
      //     "January",
      //     "February",
      //     "March",
      //     "April",
      //     "May",
      //     "June",
      //     "July",
      //     "August",
      //     "September",
      //     "October",
      //     "November",
      //     "December",
      //   ],
      //   datasets: [
      //     {
      //       label: "Data One",
      //       borderColor: "#FC2525",
      //       pointBackgroundColor: "white",
      //       borderWidth: 1,
      //       pointBorderColor: "white",
      //       backgroundColor: this.gradient,
      //       data: this.income,
      //     },
      //     {
      //       label: "Data Two",
      //       borderColor: "#05CBE1",
      //       pointBackgroundColor: "white",
      //       pointBorderColor: "white",
      //       borderWidth: 1,
      //       backgroundColor: this.gradient2,
      //       data: this.expense,
      //     },
      //   ],
      // },
      // { responsive: true, maintainAspectRatio: false }
      this.chartData,
      this.options
    );
  },
};
</script>
