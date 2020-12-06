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
      <DragChart
        :density-value="density"
        :swimmer-angle="angle"
        :swimmer="swimmer"
      />
      <SustentationChart
        :density-value="density"
        :swimmer-angle="angle"
        :swimmer="swimmer"
      />
    </div>
  </div>
</template>

<script>
import DragChart from './DragChart.vue'
import SustentationChart from './SustentationChart.vue'

export default {
  components: {
    DragChart,
    SustentationChart,
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