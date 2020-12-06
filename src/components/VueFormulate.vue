<template>
  <div class="row">
    <div class="column">
      <h2>Mecánica de la natación</h2>
      <FormulateForm v-model="formValues" @submit="handleSubmit">
        <FormulateInput
          name="swimmer"
          :options="{
            first: 'Nadador 1',
            second: 'Nadador 2',
            third: 'Nadador 3',
            fourth: 'Nadador 4'
            }"
          value="first"
          type="radio"
          label="Selecciona el nadador"
        />
        <FormulateInput
          label="Densidad del medio"
          type="range"
          name="densityValue"
          min="700"
          max="1240"
          :value="density"
          show-value="true"
        />
        <FormulateInput
          label="Ángulo del nadador"
          type="range"
          name="swimmerAngle"
          min="0"
          max="50"
          :value="angle"
          show-value="true"
        />
        <FormulateInput type="submit" label="Submit"/>
      </FormulateForm>
    </div>
    <div class="column">
      <GraphComponent
        :data="dragForceData"
        title="Fuerza de arrastre"
      />
      <GraphComponent
        :data="sustainForceData"
        title="Fuerza de sustentación"
      />
    </div>
  </div>
</template>

<script>
import GraphComponent from './GraphComponent.vue'

export default {
  components: {
    GraphComponent,
  },
  data() {
    return {
      formValues: {},
      density: 1000,
      angle: 10,
      swimmerOptions: {
        first: {
          length: 1.7,
          width: 0.3,
          top: 0.2,
        },
        second: {
          length: 1.8,
          width: 0.5,
          top: 0.4,
        },
        third: {
          length: 1.65,
          width: 0.25,
          top: 0.2,
        },
        fourth: {
          length: 1.65,
          width: 0.4,
          top: 0.3,
        },
      },
      swimmer: {
          length: 1.8,
          width: 0.5,
          top: 0.4,
        },
      };
  },
  methods: {
    handleSubmit() {
      this.density = parseInt(this.formValues.densityValue);
      this.angle = parseInt(this.formValues.swimmerAngle);
      this.swimmer = this.swimmerOptions[this.formValues.swimmer];
    },
  },
  computed: {
    dragForceData() { 
      let points = [];
      let labels = [];
      let proyectedLength = this.swimmer.length * Math.sin(this.angle * Math.PI / 180)
      proyectedLength += this.swimmer.top * Math.cos(this.angle * Math.PI / 180);
      let proyectedSurface = proyectedLength * this.swimmer.width;

      for (let i = 0; i < 2.5; i+=0.02) {
        let dragCoefficient = 0.7156 - 0.1480 * i;

        points.push((this.density * dragCoefficient * proyectedSurface * i ** 2) / 2)
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
    sustainForceData() { 
      let points = [];
      let labels = [];
      let proyectedLength = this.swimmer.length * Math.sin(this.angle * Math.PI / 180)
      proyectedLength += this.swimmer.top * Math.cos(this.angle * Math.PI / 180);
      let proyectedSurface = proyectedLength * this.swimmer.width;

      let sustainCoefficient = 0.2 + 0.4 / 45 * this.angle;
      if (this.angle > 45) { sustainCoefficient = 1.1 - 0.5 / 45 * this.angle }

      for (let i = 0; i < 2.5; i+=0.02) {
        points.push((this.density * sustainCoefficient * proyectedSurface * i ** 2) / 2)
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
  },
}
</script>

<style>
.column {
  float: left;
  width: 50%;
  text-align: left;
}

.row:after {
  content: "";
  display: table;
  clear: both;
}
</style>