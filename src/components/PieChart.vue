<template>
  <div>
    <svg :width="width" :height="height">
      <g :transform="`translate(${width / 2}, ${height / 2})`">
        <g v-for="(slice, index) in slices" :key="index">
          <path
            :d="createArc(outerRadius, innerRadius, slice.startAngle, slice.endAngle)"
            :fill="slice.color"
            @mouseover="showValues(slice.value, slice.date)"
            @mouseout="hideValues"
            class="chart-piece"
          ></path>
          <g v-if="!showHoverValues">
            <text v-if="nameIsShort(name)" text-anchor="middle" font-size="14" dy="7">{{ name }}</text>

            <g v-if="!nameIsShort(name)">
              <text text-anchor="middle" font-size="14" dy="-3">{{ getPart(name, 0) }}</text>
              <text text-anchor="middle" font-size="14" dy="20">{{ getPart(name, 1) }}</text>
            </g>
          </g>
          <g v-if="showHoverValues">
            <text text-anchor="middle" font-size="15" dy="0">{{ hoverValue }}</text>
            <text text-anchor="middle" font-size="11" dy="20">{{ hoverDate }}</text>
          </g>
        </g>
      </g>
    </svg>
  </div>
</template>

<script>
export default {
  props: {
    name: String,
    positive: {
      type: Boolean,
      default: true,
    },
    width: {
      type: Number,
      default: 160,
    },
    height: {
      type: Number,
      default: 160,
    },
    outerRadius: {
      type: Number,
      default: 70,
    },
    innerRadius: {
      type: Number,
      default: 45,
    },
    data: {
      type: Object,
      required: false,
      default: () => ({
        '0000-00-00': 1,
      }),
    },
  },
  data() {
    return {
      pieData: {},
      slices: [],
      showHoverValues: false,
      hoverValue: '',
      hoverDate: '',
    };
  },
  mounted() {
    this.checkData();
  },
  methods: {
    checkData() {
      if (!this.data || Object.keys(this.data).length === 0) {
        this.pieData = { '0000-00-00': 1 };
      } else {
        this.pieData = { ...this.data };
      }
      this.createPieChart();
    },
    createPieChart() {
      const dataValues = Object.values(this.pieData);
      const dataKeys = Object.keys(this.pieData);
      const total = dataValues.reduce((acc, val) => acc + val, 0);
      let startAngle = 0;

      this.slices = dataValues.map((value, index) => {
        const slice = {
          value,
          date: dataKeys[index],
          color: this.getColor(dataKeys[index]),
          startAngle: (Math.PI * 2 * startAngle) / total,
          endAngle: (Math.PI * 2 * (startAngle + value)) / total,
        };
        startAngle += value;
        return slice;
      });
    },
    createArc(outerRadius, innerRadius, startAngle, endAngle) {
      const startOuter = this.calculate(outerRadius, endAngle);
      const endOuter = this.calculate(outerRadius, startAngle);
      const startInner = this.calculate(innerRadius, endAngle);
      const endInner = this.calculate(innerRadius, startAngle);
      const largeArcFlag = endAngle - startAngle <= Math.PI ? '0' : '1';

      const d = [
        'M',
        startOuter.x,
        startOuter.y,
        'A',
        outerRadius,
        outerRadius,
        0,
        largeArcFlag,
        0,
        endOuter.x,
        endOuter.y,
        'L',
        endInner.x,
        endInner.y,
        'A',
        innerRadius,
        innerRadius,
        0,
        largeArcFlag,
        1,
        startInner.x,
        startInner.y,
        'Z',
      ].join(' ');

      return d;
    },
    calculate(radius, angleInRadians) {
      const x = radius * Math.cos(angleInRadians);
      const y = radius * Math.sin(angleInRadians);
      return { x, y };
    },
    getColor(date) {
      // const dataKeys = Object.keys(this.pieData);
      // const oldestDate = new Date(Math.min(...dataKeys.map((key) => new Date(key)))).getTime();
      // const newestDate = new Date(Math.max(...dataKeys.map((key) => new Date(key)))).getTime();
      // const targetDate = new Date(date).getTime();

      const daysDifference = Math.floor((new Date() - new Date(date)) / (1000 * 60 * 60 * 24));

      // const timeRange = newestDate - oldestDate;
      // const targetTimePassed = targetDate - oldestDate;
      // const percentagePassed = (targetTimePassed / timeRange) * 100;

      let lightness;
      let hue;

      if (this.positive) {
        if (daysDifference <= 2) {
          lightness = 50 - (daysDifference * 5);
          hue = 120;
        } else if (daysDifference >= 3 && daysDifference <= 7) {
          let value = daysDifference - 3;
          lightness = 70 - (value * 5);
          hue = 30;
        } else {
          let value = daysDifference - 8;
          lightness = 80 - (value * 5);
          hue = 0;
        }
      } else {
        if (daysDifference <= 2) {
          lightness = 70 - (daysDifference * 5);
          hue = 0;
        } else if (daysDifference >= 3 && daysDifference <= 7) {
          let value = daysDifference - 3;
          lightness = 70 - (value * 5);
          hue = 30;
        } else {
          let value = daysDifference - 8;
          lightness = 50 - (value * 5);
          hue = 120;
        }
      }

      return `hsl(${hue}, 100%, ${lightness}%)`;
    },
    showValues(value, date) {
      this.showHoverValues = true;
      this.hoverValue = value;
      const newDate = new Date(date);
      this.hoverDate = `${newDate.getFullYear()}/${(newDate.getMonth() + 1)}/${newDate.getDate()}`;
    },
    hideValues() {
      this.showHoverValues = false;
      this.hoverValue = '';
      this.hoverDate = '';
    },
    nameIsShort(name) {
      if (name.includes('_')) {
        if (name.length > 12) {
          return false;
        }
      }
      return true;
    },
    getPart(str, i) {
      if (!this.nameIsShort(str)) {
        const words = str.split('_');
        return words[i];
      }
    },
  },
};
</script>

<style scoped>
.chart-piece {
  transition: transform 0.2s ease-out;
}

.chart-piece:hover {
  transform: scale(1.05);
}
</style>
