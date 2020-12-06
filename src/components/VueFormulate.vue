<template>
  <div class="row">
    <h1>Mecánica de la natación</h1>
    <br>
    <div class="column">
      <div class="notification-container">
        <h2 class="subtitle">Fuerza de arrastre</h2>
        <img
          src="../assets/question.png"
          width="20px"
          height="20px"
          @click="() => {dragVisibility = !dragVisibility}"
        />
        <div
          id="swimmers-info"
          class="notification drag-info formulate-input"
          v-show="dragVisibility"
        >
          <p>La fuerza de arrastre actua en el sentido opuesto</p>
          <p>al movimiento y se puede calcular como:</p>
          <p>D = CD p S v<sup>2</sup>/2</p>
          <p>Donde CD es el coeficiente de roce, p la densidad</p>
          <p>del fluido, v la velocidad y S la superficie proyectada</p>
          <p>perpendicular al movimiento.</p>
        </div>
      </div>
      <div class="notification-container">
        <h2 class="subtitle">Fuerza de sustentación</h2>
        <img
          src="../assets/question.png"
          width="20px"
          height="20px"
          @click="() => {sustainVisibility = !sustainVisibility}"
        />
        <div
          id="swimmers-info"
          class="notification sustain-info formulate-input"
          v-show="sustainVisibility"
        >
          <p>La fuerza de sustentación actua de forma perpendicular</p>
          <p>al movimiento y se puede calcular como:</p>
          <p>D = CL p S v<sup>2</sup>/2</p>
          <p>Donde CL es el coeficiente de sustentación, p la densidad</p>
          <p>del fluido, v la velocidad y S la superficie proyectada</p>
          <p>perpendicular al movimiento.</p>
        </div>
      </div>
      <FormulateForm v-model="formValues" @submit="handleSubmit">
        <div class="swimmers-notification-container">
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
          <img
            src="../assets/question.png"
            width="20px"
            height="20px"
            @click="() => {showSwimmersInfo = !showSwimmersInfo}"
          />
          <div
            class="notification swimmers-info formulate-input"
            v-show="showSwimmersInfo"
          >
            <p>Nadador 1. Altura: 170 cm. Masa: 75 kg.</p>
            <p>Nadador 2. Altura: 180 cm. Masa: 83 kg.</p>
            <p>Nadador 3. Altura: 165 cm. Masa: 60 kg.</p>
            <p>Nadador 4. Altura: 165 cm. Masa: 72 kg.</p>
          </div>
        </div>
        <FormulateInput
          label="Densidad del medio (kg/m3)"
          type="range"
          name="densityValue"
          min="700"
          max="1240"
          :value="density"
          show-value="true"
        />
        <FormulateInput
          label="Ángulo del nadador (grados)"
          type="range"
          name="swimmerAngle"
          min="0"
          max="50"
          :value="angle"
          show-value="true"
        />
        <FormulateInput type="submit" label="Calcular"/>
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
      showSwimmersInfo: false,
      dragVisibility: false,
      sustainVisibility: false,
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

.notification-container {
  display: flex;
  align-items: center;
}

.swimmers-notification-container {
  display: flex;
}

.notification {
  position: absolute;
  z-index: 99;
  background-color: rgb(211, 204, 205);
  padding: 0 10px;
  border-radius: 20px;
  text-align: center;
}

.swimmers-info {
  left: 15%;
}

.drag-info {
  left: 20%;
  top: 24%;
}

.sustain-info {
  left: 24%;
  top: 35%;
}

.subtitle {
  margin: 15px 0;
}

</style>