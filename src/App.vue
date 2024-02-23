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
        '2024-02-22': 100,
        '2024-02-21': 100,
        '2024-02-20': 100,
        '2024-02-19': 100,
        '2024-02-18': 100,
        '2024-02-17': 100,
        '2024-02-16': 100,
        '2024-02-15': 100,
        '2024-02-14': 100,
        '2024-02-13': 100,
        '2024-02-12': 100,
        '2024-02-11': 100,
        '2024-02-10': 100,
        '2024-02-09': 100,
        '2024-02-08': 100,
        '2024-02-07': 100,
        '2024-02-06': 100,
        '2024-02-05': 100,
        '2024-02-04': 100,
        '2024-02-03': 100,
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
