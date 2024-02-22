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
      immediate: true // This ensures the watcher is triggered immediately upon component creation
    }
  },
  methods: {
    updatePieChart() {
      const dataValues = Object.values(this.validatedData);
      const dataKeys = Object.keys(this.validatedData);
      let startAngle = 0;

      const sortedData = dataKeys.map((date, index) => ({
        date, value: dataValues[index],
      })).sort((a, b) => new Date(b.date) - new Date(a.date));

      const total = sortedData.reduce((acc, { value }) => acc + value, 0);
      this.slices = sortedData.map(({ value, date }) => {
        const slice = {
          value,
          date,
          color: this.getColor(date),
          startAngle: this.round((Math.PI * 2 * startAngle) / total),
          endAngle: this.round((Math.PI * 2 * (startAngle + value)) / total),
        };
        startAngle += value;
        return slice;
      });
    },
    round(value) {
      return Math.round(value * 1e3) / 1e3;
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
      const daysDifference = Math.floor((new Date() - new Date(date)) / (1000 * 60 * 60 * 24));
      let lightness;
      let hue;

      if (this.positive) {
        if (daysDifference <= 2) {
          lightness = 50 - (daysDifference * 5);
          hue = 120;
        } else if (daysDifference >= 3 && daysDifference <= 7) {
          const value = daysDifference - 3;
          lightness = 70 - (value * 5);
          hue = 30;
        } else {
          const value = daysDifference - 8;
          lightness = 70 - (value * 2.5);
          hue = 0;
        }
      }

      if (!this.positive) {
        if (daysDifference <= 2) {
          lightness = 70 - (daysDifference * 5);
          hue = 0;
        } else if (daysDifference >= 3 && daysDifference <= 7) {
          const value = daysDifference - 3;
          lightness = 70 - (value * 5);
          hue = 30;
        } else {
          const value = daysDifference - 8;
          lightness = 50 - (value * 2);
          hue = 120;
        }
      }

      return `hsl(${hue}, 100%, ${lightness}%)`;
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
