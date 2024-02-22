<template>
  <svg class="piechart" :width="width" :height="height">
    <g :transform="`translate(${width / 2}, ${height / 2})`">
      <g v-for="(slice, index) in slices" :key="index">
        <g class="piece"
          @mouseover="showValues(slice.value, slice.date)"
          @mouseout="hideValues">

          <path
            :d="createArc(outerRadius, innerRadius, slice.startAngle, slice.endAngle)"
            :fill="slice.color"
            class="hover-piece"
          ></path>

          <path
            :d="createArc(outerRadius, innerRadius, slice.startAngle, slice.endAngle)"
            :fill="slice.color"
          ></path>
        </g>
      </g>

      <g v-if="!showHoverValues">
        <text v-if="nameIsShort(name)" text-anchor="middle" :font-size="fontSize" dy="7" font-family="Arial">{{ name }}</text>

        <g v-if="!nameIsShort(name)">
            <text v-for="(part, index) in getParts(name)"
              :key="index"
              text-anchor="middle"
              :font-size="fontSize"
              :dy="getDY(getParts(name).length, index)" font-family="Arial">
              {{ part }}
            </text>
        </g>
      </g>

      <g v-if="showHoverValues">
        <text text-anchor="middle" :font-size="fontSize" dy="0" font-family="Arial">{{ hoverValue }}</text>
        <text text-anchor="middle" :font-size="fontSize" dy="20" font-family="Arial">{{ hoverDate }}</text>
      </g>
    </g>
  </svg>
</template>

<script>
import { scaleLinear } from 'd3-scale';

export default {
  props: {
    name: {
      type: String,
      default: '',
    },
    fontSize: {
      type: Number,
      default: 16,
    },
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
      slices: [],
      showHoverValues: false,
      hoverValue: '',
      hoverDate: '',
    };
  },
  computed: {
    validatedData() {
      if (!this.data || Object.keys(this.data).length === 0 || Object.values(this.data).every(value => value === 0)) {
        return { '0000-00-00': 1 };
      } else {
        return { ...this.data };
      }
    },
  },
  mounted() {
    this.updatePieChart();
  },
  watch: {
    validatedData: {
      handler() {
        this.updatePieChart();
      },
    },
  },
  methods: {
    updatePieChart() {
      const data = this.array(this.sortedData());
      const total = data.reduce((total, { value }) => total + value, 0);

      this.slices = data.map((item, index) => {
        const startAngle = this.calculateStartAngle(data, index, total);
        const endAngle = this.calculateEndAngle(data, index, total);

        return {
          value: item.value,
          date: item.date,
          color: this.getMappedColorForDays(item.date),
          startAngle,
          endAngle,
        };
      });
    },
    sortedData() {
      return Object.entries(this.validatedData)
        .sort((a, b) => new Date(b[0]) - new Date(a[0]))
        .map(([date, value]) => ({ date, value }));
    },
    calculateStartAngle(data, index, total) {
      if (index === 0) return 0;
      return this.roundf((Math.PI * 2 * data.slice(0, index).reduce((acc, { value }) => acc + value, 0)) / total);
    },
    calculateEndAngle(data, index, total) {
      return this.roundf((Math.PI * 2 * (data.slice(0, index + 1).reduce((acc, { value }) => acc + value, 0))) / total);
    },
    array(data) {
      if (Array.isArray(data)) {
        return data;
      } else {
        console.error("Data is not an array:", data);
        return [];
      }
    },
    roundf(value) {
      return Math.round(value * 1e3) / 1e3;
    },
    createArc(outerRadius, innerRadius, startAngle, endAngle) {
      const startOuter = this.calculatePoint(outerRadius, endAngle);
      const endOuter = this.calculatePoint(outerRadius, startAngle);
      const startInner = this.calculatePoint(innerRadius, endAngle);
      const endInner = this.calculatePoint(innerRadius, startAngle);
      const largeArcFlag = endAngle - startAngle <= Math.PI ? '0' : '1';

      const d = `
        M ${startOuter.x} ${startOuter.y}
        A ${outerRadius} ${outerRadius} 0 ${largeArcFlag} 0 ${endOuter.x} ${endOuter.y}
        L ${endInner.x} ${endInner.y}
        A ${innerRadius} ${innerRadius} 0 ${largeArcFlag} 1 ${startInner.x} ${startInner.y}
        Z,
      `;
      return d;
    },
    calculatePoint(radius, angleInRadians) {
      const x = radius * Math.cos(angleInRadians);
      const y = radius * Math.sin(angleInRadians);
      return { x, y };
    },
    getMappedColorForDays(date) {
      const daysDifference = Math.floor((new Date() - new Date(date)) / (1000 * 60 * 60 * 24));

      let colorScale;
      if (this.positive) {
        colorScale = scaleLinear()
          .domain([0, 10, 14])
          .range(['green', 'orange', 'red']);
      } else {
         colorScale = scaleLinear()
          .domain([0, 10, 14])
          .range(['red', 'orange', 'green']);
      }

      return colorScale(daysDifference);
    },
    showValues(value, date) {
      this.showHoverValues = true;
      this.hoverValue = value;
      const newDate = new Date(date);

      if (!Number.isNaN(newDate.getTime())) {
        this.hoverDate = `${newDate.getFullYear()}/${newDate.getMonth() + 1}/${newDate.getDate()}`;
      } else {
        this.hoverDate = '0000-00-00';
      }
    },
    hideValues() {
      this.showHoverValues = false;
      this.hoverValue = '';
      this.hoverDate = '';
    },
    nameIsShort(name) {
      if (name.includes('_')) {
        if (name.length > (this.width / 15) - this.fontSize) {
          return false;
        }
      }
      return true;
    },
    getParts(str) {
      if (!this.nameIsShort(str)) {
        const substrings = str.split('_');
        const result = [];
        let currentSubstring = '';

        for (let i = 0; i < substrings.length; i++) {
          const substring = substrings[i];
          const isShortText = currentSubstring.length + substring.length + 1 < (this.width / 15) - (this.fontSize / 2);
          if (isShortText) {
            if (currentSubstring !== '') {
              currentSubstring += `_${substring}`;
            } else {
              currentSubstring = substring;
            }
          }
          if (!isShortText) {
            if (currentSubstring !== '') {
              result.push(currentSubstring);
              currentSubstring = substring;
            } else {
              currentSubstring = substring;
            }
          }
        }

        if (currentSubstring !== '') {
          result.push(currentSubstring);
        }

        return result;
      }
      return [];
    },
    getDY(totalParts, currentIndex) {
      const lineHeight = 20;
      const totalHeight = totalParts * lineHeight;
      const yOffset = (lineHeight / 2) - (totalHeight / 2) + 10;
      return yOffset + currentIndex * lineHeight;
    },
  },
};
</script>

<style scoped>
.hover-piece {
  transition: transform 0.2s ease-out;
}

.piece:hover .hover-piece {
  transform: scale(1.07);
}
</style>
