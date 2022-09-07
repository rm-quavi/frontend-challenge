<template>
  <div class="chart">
    <div class="heading-container">
      <div class="heading-wrapper">
        <h2 class="load-name">{{ loadProfile.name }}</h2>
        <p class="chart-name">Load, daily view</p>

        <h6 class="chart-unit">kW</h6>
      </div>
      <b-form-select 
        v-model="selectedSeason" 
        :options="seasons"
        @change="processDataset()"
      >
        <template #button-content>
          hi
        </template>
      </b-form-select>
    </div>
    <LineChart
      :chartData="chartData"
      :chartOptions="chartOptions"
    ></LineChart>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import LineChart from './LineChart.vue';
import loadDataset from '~/data/load-dataset.json'

export default Vue.extend({
  components: { LineChart },
  props: ['loadProfile'],
  data() {
    return {
      selectedSeason: null,
      seasons: [
        {
          text: "Show all seasons",
          value: null
        },
        {
          text: 'Summer',
          value: 'su'
        },
        {
          text: 'Autumn',
          value: 'au'
        },
        {
          text: 'Winter',
          value: 'wi'
        },
        {
          text: 'Spring',
          value: 'sp'
        },
      ],
      chartData: {},
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            display: false
          },
        },
        scales: {
          y: {
            ticks: {
              callback: function(value, index, ticks) {
                return (value / 1000) + 'k'
              }
            },
            grid: {
              drawBorder: false
            }
          },
          x: {
            grid: {
              display: false
            },
            ticks: {
              callback: function(value, index, ticks) {
                let time = this.getLabelForValue(value)
                let hour = Math.floor(time / 60).toString()
                if (hour.length == 1) hour = '0' + hour
                let minute = (time % 60).toString()
                if (minute != '30') minute = '00'

                if (index % 5 == 0) return hour + ':' + minute
              }
            }
          },
        }
      }
    }
  },
  computed: {
    filterLoads() {
      
    }
  },
  mounted() {
    this.processDataset()
  },
  methods: {
    processDataset() {
      let chartData = {
        labels: loadDataset.chunked_time_series.x_axis,
        datasets: []
      }

      // Filter days
      let days = []
      if (this.selectedSeason != null) {
        days = loadDataset.chunked_time_series.days.filter(day => day.season == this.selectedSeason)
      } else {
        days = loadDataset.chunked_time_series.days
      }

      // Calculate total average
      let totalAverage = []
      for (let ctr = 0 ; ctr < loadDataset.chunked_time_series.x_axis.length ; ctr++) {
        let average = 0
        for (let day of days) {
          average = average + day.values[ctr]
        }
        totalAverage.push(average / days.length)
      }
      
      // Insert total average to chartdata
      chartData.datasets.push({
        borderColor: '#102D4C',
        pointRadius: 0,
        borderWidth: 4,
        data: totalAverage,
      })
      
      // Insert all dataset to chartdata
      days.map(day => {
        chartData.datasets.push({
          borderColor: '#c6c6c6',
          pointRadius: 0,
          borderWidth: 1,
          data: day.values
        })
      })

      this.chartData = chartData
    }
  }
})
</script>

<style scoped lang="scss">
.chart {
  padding: 16px;

  .heading-container {
    color: $c-primary;
    display: flex;
    justify-content: space-between;

    .load-name {
      font-weight: bold;
      font-size: 22px;
      margin-bottom: 0;
    }

    .chart-name {
      font-weight: 18px;
    }

    .chart-unit {
      font-weight: bold;
      font-size: 16px;
    }

    .custom-select {
      max-width: 155px;
      border: none;
      color: $c-accent;
      border-radius: 12px;
      font-size: 14px;
      -webkit-appearance: none;
      -moz-appearance: none;
      background: white;
      background-image: url("data:image/svg+xml;utf8,<svg height='24' width='24' viewBox='0 0 24 24' xmlns='http://www.w3.org/2000/svg'><path fill='DodgerBlue' d='M7.41,8.58L12,13.17L16.59,8.58L18,10L12,16L6,10L7.41,8.58Z' /></svg>");
      background-repeat: no-repeat;
      background-position-x: calc(100% - 5px);
      background-position-y: 5px;
    }
  }
}

@media screen and (min-width: 768px) {
  .chart {
    padding: 20px 28px 60px;

    .load-name {
      font-size: 28px;
    }
  }
}
</style>