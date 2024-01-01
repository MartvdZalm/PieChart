<template>

  <PieChart
    name="Brands"
    :width="350"
    :height="350"
    :outerRadius="140"
    :innerRadius="70"
    :data="slices"
    />

  <PieChart
    :width="350"
    :height="350"
    :outerRadius="140"
    :innerRadius="70"
    />

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
        '2024-01-01': 324546,
        '2023-12-18': 435264,
        '2023-12-29': 413524,
        '2023-12-12': 134524,
        '2023-12-17': 124524,
        '2023-12-23': 334524,
        '2023-12-16': 354524,
        '2023-12-14': 294524,
      },
    };
  },
}
</script>

<style>

</style>
