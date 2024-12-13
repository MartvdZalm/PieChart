<template>
  <div>
    <DonutChart
      title="Categories"
      :data="slices"
    />

    <PieChart
      title="Categories"
      :data="slices"
    />

    <button @click="updateData">Update Data</button>
  </div>
</template>

<script>
import DonutChart from './components/DonutChart.vue'
import PieChart from './components/PieChart.vue'

export default {
  name: 'App',
  components: {
    DonutChart,
    PieChart
  },
  data() {
    return {
      slices: {
        '2024-12-13': 1000,
        '2024-12-12': 13,
        '2024-12-11': 12,
        '2024-12-10': 1,
        '2024-12-09': 42,
        '2024-12-08': 12,
        '2024-12-07': 20,
        '2024-12-06': 18,
        '2024-12-05': 14,
        '2024-12-04': 10,
        '2024-12-03': 20,
        '2024-12-02': 18,
        '2024-12-01': 14,
        '2024-11-30': 10,
        '2024-11-29': 20,
        '2024-11-28': 18,
        '2024-11-27': 14,
        '2024-11-26': 10,
      },
    };
  },
  methods: {
    updateData() {
      // Example: Randomize data values
      Object.keys(this.slices).forEach(date => {
        this.slices[date] = Math.floor(Math.random() * 100) + 1;
      });
    }
  }
}
</script>
