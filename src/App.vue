<template>

  <PieChart
    name="Brands"
    :width="350"
    :height="350"
    :outerRadius="140"
    :innerRadius="70"
    :data="slices"
    :fontSize="22"
    />

  <PieChart
    name="promotion_unknown_sale_condition_s_s"
    :width="350"
    :height="350"
    :outerRadius="140"
    :innerRadius="70"
    :data="slices"
    :positive="false"
    :fontSize="22"
    />

      <PieChart
    name="promotion_unknown_sale_condition_s"
    :width="350"
    :height="350"
    :outerRadius="140"
    :innerRadius="70"
    :data="slices"
    :positive="false"
    :fontSize="22"
    />

      <PieChart
    name="promotion_unknown_sale_condition_s"
    :width="350"
    :height="350"
    :outerRadius="140"
    :innerRadius="70"
    :data="slices"
    :positive="false"
    :fontSize="22"
    />

      <PieChart
    name="promotion_unknown_sale_condition_s"
    :width="350"
    :height="350"
    :outerRadius="140"
    :innerRadius="70"
    :data="slices"
    :positive="false"
    :fontSize="22"
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
        '2024-01-02': 100,
        '2024-01-01': 100,
        '2023-12-31': 100,
        '2023-12-18': 100,
        '2023-12-29': 100,
        '2023-12-27': 100,
        '2023-12-26': 100,
        '2023-12-12': 100,
        '2023-12-17': 100,
        '2023-12-23': 100,
        '2023-12-16': 100,
        '2023-12-14': 100,
        '2023-12-09': 100,
        '2023-12-05': 100,
        '2023-12-04': 100,
        '2023-12-03': 100,
      },
    };
  },
}
</script>

<style>

</style>
