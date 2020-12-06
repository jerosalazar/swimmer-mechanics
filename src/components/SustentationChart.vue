<script>
import { Line } from 'vue-chartjs'

export default {
  extends: Line,
  props: {
    densityValue: {
      type: Number,
      default: 0,
    },
    swimmerAngle: {
      type: Number,
      default: 0,
    },
    swimmer: {
      type: Object,
      default: () => {},
    },
  },
  watch: {
    densityValue() {
      this.renderChart(this.sustainForceData, this.options);
    },
    swimmerAngle() {
      this.renderChart(this.sustainForceData, this.options);
    },
    swimmer() {
      this.renderChart(this.sustainForceData, this.options);
    },
  },
  computed: {
    sustainForceData() { 
      let points = [];
      let labels = [];
      let swimmerLength = this.swimmer.length;
      let swimmerTop = this.swimmer.top;
      let swimmerWidth = this.swimmer.width;
      let proyectedLength = swimmerLength * Math.sin(this.swimmerAngle * Math.PI / 180) + swimmerTop * Math.cos(this.swimmerAngle * Math.PI / 180);
      let proyectedSurface = proyectedLength * swimmerWidth;
      let sustainCoefficient = 0.2 + 0.4 / 45 * this.swimmerAngle;
      if (this.swimmerAngle > 45) { sustainCoefficient = 1.1 - 0.5 / 45 * this.swimmerAngle }
      for (let i = 0; i < 2.5; i+=0.02) {
        points.push((this.densityValue * sustainCoefficient * proyectedSurface * i ** 2) / 2)
        labels.push(i.toFixed(2));
      }

      return {
        labels: labels,
        datasets: [
          {
            borderColor: '#f87979',
            pointRadius: 0,
            data: points,
          }
        ]
      };
    },
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
              labelString: "Fuerza de sustentación",
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
              return [`Velocidad: ${tooltipItem.label} m/seg`, `Fuerza de sustentación: ${parseInt(tooltipItem.value)} Nw`];
            },
          },
        },
      };
    }
  },
  mounted () {
    this.renderChart(this.sustainForceData, this.options)
  }
}
</script>