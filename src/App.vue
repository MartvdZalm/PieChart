<template>
  <div>
    <PieChart
      title="PieChart"
      :width="350"
      :height="350"
      :outerRadius="140"
      :innerRadius="70"
      :data="slices"
    />

    <button @click="updateData">Update Data</button>
  </div>
</template>

<script>
import PieChart from './components/PieChart.vue'

export default {
  name: 'App',
  components: {
    PieChart
  },
  data() {
    return {
      slices: {
        '2024-02-22': 10000,
        '2024-02-21': 13,
        '2024-02-20': 12,
        '2024-02-19': 1,
        '2024-02-18': 42,
        '2024-02-17': 12,
        '2024-02-16': 20,
        '2024-02-15': 18,
        '2024-02-14': 14,
        '2024-02-13': 10,
        '2024-02-12': 10,
        '2024-02-11': 80,
        '2024-02-10': 5,
        '2024-02-09': 2,
        '2024-02-08': 1,
        '2024-02-07': 10,
        '2024-02-06': 150,
        '2024-02-05': 1,
        '2024-02-04': 40,
        '2024-02-03': 14,
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
