<script>
import { Line } from 'vue-chartjs'

export default {
  extends: Line,
  props: {
    title: {
      type: String,
      default: '',
    },
    data: {
      type: Object,
      default: () => {},
    },
  },
  watch: {
    data() {
      this.renderChart(this.data, this.options);
    },
  },
  computed: {
    options() {
      return {
        scaleShowLabels: false,
        responsive: true,
        maintainAspectRatio: false,
        legend: {
          display: false,
        },
        scales: {
          yAxes: [{
            scaleLabel: {
              display: true,
              labelString: this.title,
            },
          }],
          xAxes: [{
            ticks: {
              display: false,
            },
            gridLines: {
              display: false,
            },
          }],
        },
        tooltips: {
          callbacks: {
            label: (tooltipItem) => {
              return [`Velocidad: ${tooltipItem.label} m/seg`, `${this.title}: ${parseInt(tooltipItem.value)} Nw`];
            },
          },
        },
      };
    }
  },
  mounted () {
    console.log(this.data);
    this.renderChart(this.data, this.options)
  }
}
</script>