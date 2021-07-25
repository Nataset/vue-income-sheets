<script>
import { Line, mixins } from "vue-chartjs";
const { reactiveProp } = mixins;

export default {
  extends: Line,
  mixins: [reactiveProp],
  props: ["chartData"],
  data() {
    return {
      gradient: null,
      gradient2: null,
      options: { responsive: true, maintainAspectRatio: false },
    };
  },
  mounted() {
    this.gradient = this.$refs.canvas
      .getContext("2d")
      .createLinearGradient(0, 0, 0, 450);
    this.gradient2 = this.$refs.canvas
      .getContext("2d")
      .createLinearGradient(0, 0, 0, 450);

    this.gradient.addColorStop(0, "rgba(255, 0,0, 0.5)");
    this.gradient.addColorStop(0.5, "rgba(255, 0, 0, 0.25)");
    this.gradient.addColorStop(1, "rgba(255, 0, 0, 0)");

    this.gradient2.addColorStop(0, "rgba(0, 231, 255, 0.9)");
    this.gradient2.addColorStop(0.5, "rgba(0, 231, 255, 0.25)");
    this.gradient2.addColorStop(1, "rgba(0, 231, 255, 0)");

    this.chartData.datasets[0].backgroundColor = this.gradient;
    this.chartData.datasets[1].backgroundColor = this.gradient2;

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
