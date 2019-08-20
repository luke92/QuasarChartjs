<template>
  <q-page padding>
    <q-form @submit="buscarResultados" class="q-pa-md">
      <q-input
        ref="nroHistoriaClinica"
        v-model="nroHistoriaClinica"
        type="number"
        filled
        style="max-width: 200px"
        label="Historia Clínica"
        lazy-rules
        :rules="[
          val => val !== null && val !== '' || 'Ingrese el número de historia clínica',
          val => val > 0 || 'El número de historia clínica no puede ser menor a 1'
        ]"
      />

      <q-btn
        align="right"
        type="submit"
        :loading="submitting"
        label="Buscar Resultados de Laboratorio"
        class="q-mt-md"
        color="teal"
      >
        <template v-slot:loading>
          <q-spinner-facebook />
        </template>
      </q-btn>
    </q-form>

    <div v-if="showTable" class="q-pa-md">
      <q-table
        :pagination.sync="pagination"
        :dense="$q.screen.lt.md"
        title="Treats"
        :data="data"
        :columns="columns"
        row-key="name"
      />
      <div class="q-pa-md" style="max-width: 300px">
        <div class="q-gutter-md">
          <q-select
            filled
            v-model="variable"
            :options="optionsSelectVariable"
            label="Variable"
            @input="val => { onValueChange() }"
          />
        </div>
      </div>
    </div>
    <div style="height: 400px">
      <canvas id="lineChart" class="full-width" style="height: 350px;" v-show="showChart"></canvas>
    </div>
  </q-page>
</template>

<script>
import Chart from 'chart.js';

export default {
  mounted() {
    this.drawTheChart();
    this.streamData();
  },
  data() {
    return {
      variable: null,
      optionsSelectVariable: [
        'Google',
        'Facebook',
        'Twitter',
        'Apple',
        'Oracle',
      ],
      mLineChart: null,
      pagination: {
        page: 1,
        rowsPerPage: 0, // 0 means all rows
      },
      loaded: true,
      showTable: false,
      showChart: false,
      nroHistoriaClinica: '',
      submitting: false,
      columns: [
        {
          name: 'name',
          required: true,
          label: 'Dessert (100g serving)',
          align: 'left',
          field: row => row.name,
          format: val => `${val}`,
          sortable: true,
        },
        {
          name: 'calories',
          align: 'center',
          label: 'Calories',
          field: 'calories',
          sortable: true,
        },
        {
          name: 'fat',
          label: 'Fat (g)',
          field: 'fat',
          sortable: true,
        },
        { name: 'carbs', label: 'Carbs (g)', field: 'carbs' },
        { name: 'protein', label: 'Protein (g)', field: 'protein' },
        { name: 'sodium', label: 'Sodium (mg)', field: 'sodium' },
        {
          name: 'calcium',
          label: 'Calcium (%)',
          field: 'calcium',
          sortable: true,
          sort: (a, b) => parseInt(a, 10) - parseInt(b, 10),
        },
        {
          name: 'iron',
          label: 'Iron (%)',
          field: 'iron',
          sortable: true,
          sort: (a, b) => parseInt(a, 10) - parseInt(b, 10),
        },
      ],
      data: [
        {
          name: 'Frozen Yogurt',
          calories: 159,
          fat: 6.0,
          carbs: 24,
          protein: 4.0,
          sodium: 87,
          calcium: '14',
          iron: '1',
        },
        {
          name: 'Ice cream sandwich',
          calories: 237,
          fat: 9.0,
          carbs: 37,
          protein: 4.3,
          sodium: 129,
          calcium: '8',
          iron: '1',
        },
        {
          name: 'Eclair',
          calories: 262,
          fat: 16.0,
          carbs: 23,
          protein: 6.0,
          sodium: 337,
          calcium: '6',
          iron: '7',
        },
        {
          name: 'Cupcake',
          calories: 305,
          fat: 3.7,
          carbs: 67,
          protein: 4.3,
          sodium: 413,
          calcium: '3',
          iron: '8',
        },
        {
          name: 'Gingerbread',
          calories: 356,
          fat: 16.0,
          carbs: 49,
          protein: 3.9,
          sodium: 327,
          calcium: '7',
          iron: '16',
        },
        {
          name: 'Jelly bean',
          calories: 375,
          fat: 0.0,
          carbs: 94,
          protein: 0.0,
          sodium: 50,
          calcium: '0',
          iron: '0',
        },
        {
          name: 'Lollipop',
          calories: 392,
          fat: 0.2,
          carbs: 98,
          protein: 0,
          sodium: 38,
          calcium: '0',
          iron: '2',
        },
        {
          name: 'Honeycomb',
          calories: 408,
          fat: 3.2,
          carbs: 87,
          protein: 6.5,
          sodium: 562,
          calcium: '0',
          iron: '45',
        },
        {
          name: 'Donut',
          calories: 452,
          fat: 25.0,
          carbs: 51,
          protein: 4.9,
          sodium: 326,
          calcium: '2',
          iron: '22',
        },
        {
          name: 'KitKat',
          calories: 518,
          fat: 26.0,
          carbs: 65,
          protein: 7,
          sodium: 54,
          calcium: '12',
          iron: '6',
        },
      ],
    };
  },
  methods: {
    onValueChange() {
      this.showChart = true;
      this.$q.notify({
        color: 'green-5',
        textColor: 'white',
        icon: 'fas fa-exclamation-triangle',
        message: 'Selected Value: '.concat(this.variable.value),
      });
      this.mLineChart.data.datasets[0].label = this.variable.label;
      this.mLineChart.data.labels = [];
      this.data.forEach((item) => {
        this.mLineChart.data.labels.push(item.name);
      });
      this.mLineChart.data.datasets[0].data = [];
      this.data.forEach((item) => {
        this.mLineChart.data.datasets[0].data.push(item[this.variable.value]);
      });
      this.mLineChart.update();
    },
    buscarResultados() {
      if (this.nroHistoriaClinica >= 1) {
        this.submitting = true;
        this.showTable = true;
        this.optionsSelectVariable = [];
        this.columns.forEach((item, index) => {
          if (index === 0) return;
          this.optionsSelectVariable.push({
            label: item.label,
            value: item.name,
          });
        });
      } else {
        this.submitting = false;
        this.showTable = false;
        this.$q.notify({
          color: 'red-5',
          textColor: 'white',
          icon: 'fas fa-exclamation-triangle',
          message: 'Debe ingresar el número de historia clínica',
        });
      }

      // Simulating a delay here.
      // When we are done, we reset "submitting"
      // Boolean to false to restore the
      // initial state.
      setTimeout(() => {
        // delay simulated, we are done,
        // now restoring submit to its initial state
        this.submitting = false;
      }, 1000);
    },
    drawTheChart() {
      const ctx2 = document.getElementById('lineChart').getContext('2d');
      this.mLineChart = new Chart(ctx2, {
        type: 'line',
        data: {
          labels: [
            this.data[1].name,
            this.data[2].name,
            this.data[3].name,
            this.data[4].name,
          ],
          datasets: [
            {
              label: 'Calories',
              data: [
                this.data[1].calories,
                this.data[2].calories,
                this.data[3].calories,
                this.data[4].calories,
              ],
              backgroundColor: 'rgba(129, 212, 250, 0.2)',
              borderColor: 'rgba(129, 212, 250, 1)',
              borderWidth: 1,
            },
          ],
        },
        options: {
          maintainAspectRatio: false,
        },
      });
    },
  },
};
</script>

<style>
</style>
