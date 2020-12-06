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
      this.renderChart(this.dragForceData, this.options);
    },
    swimmerAngle() {
      this.renderChart(this.dragForceData, this.options);
    },
    swimmer() {
      this.renderChart(this.dragForceData, this.options);
    },
  },
  computed: {
    dragForceData() { 
      let points = [];
      let labels = [];
      let swimmerLength = this.swimmer.length;
      let swimmerTop = this.swimmer.top;
      let swimmerWidth = this.swimmer.width;
      let proyectedLength = swimmerLength * Math.sin(this.swimmerAngle * Math.PI / 180) + swimmerTop * Math.cos(this.swimmerAngle * Math.PI / 180);
      let proyectedSurface = proyectedLength * swimmerWidth;
      for (let i = 0; i < 2.5; i+=0.02) {
        let dragCoefficient = 0.7156 - 0.1480 * i;
        points.push((this.densityValue * dragCoefficient * proyectedSurface * i ** 2) / 2)
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
              labelString: "Fuerza de arrastre",
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
              return [`Velocidad: ${tooltipItem.label} m/seg`, `Fuerza de roce: ${parseInt(tooltipItem.value)} Nw`];
            },
          },
        },
      };
    }
  },
  mounted () {
    this.renderChart(this.dragForceData, this.options)
  }
}
</script>