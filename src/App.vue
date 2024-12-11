<template>
  <div>
    <DonutChart
      title="Categories"
      :data="slices"
    />

    <button @click="updateData">Update Data</button>
  </div>
</template>

<script>
import DonutChart from './components/DonutChart.vue'

export default {
  name: 'App',
  components: {
    DonutChart
  },
  data() {
    return {
      slices: {
        '2024-12-11': 1000,
        '2024-12-10': 13,
        '2024-12-09': 12,
        '2024-12-08': 1,
        '2024-11-07': 42,
        '2024-11-28': 12,
        '2024-11-27': 20,
        '2024-11-26': 18,
        '2024-11-25': 14,
        '2024-11-24': 10,
        '2024-11-23': 20,
        '2024-11-22': 18,
        '2024-11-21': 14,
        '2024-11-20': 10,
        '2024-11-19': 20,
        '2024-11-18': 18,
        '2024-11-17': 14,
        '2024-11-16': 10,
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
