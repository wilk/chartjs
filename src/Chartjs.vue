<template>
  <canvas v-if="componentDidMount" class="chartjs" :width="width" :height="height"></canvas>
</template>

<script>
const types = ['line', 'bar', 'radar', 'polarArea', 'pie', 'doughnut']

export default {

  props: {
    width: Number,
    height: Number,
    type: {
      type: String,
      required: true,
      validator: val => types.includes(val)
    },
    data: {
      type: Object,
      required: true,
      default: () => ({})
    },
    options: {
      type: Object,
      default: () => ({})
    }
  },
  
  beforeMount() {
    // needed for SSR
    this.ChartJS = require('chart.js') // With moment.js
  },

  mounted () {
    this.chart = new this.ChartJS(this.$el, {
      type: this.type,
      data: this.data,
      options: this.options
    })
    
    this.componentDidMount = true
  },

  data () {
    return {
      componentDidMount: false,
      ChartJS: null,
      chart: null
    }
  },

  methods: {
    resetChart () {
      this.$nextTick(() => {
        this.chart.destroy()
        this.chart = new this.ChartJS(this.$el, {
          type: this.type,
          data: this.data,
          options: this.options
        })
      })
    }
  },

  watch: {
    type () {
      this.resetChart()
    },
    data () {
      this.chart.update()
    },
    options () {
      this.resetChart()
    }
  }
}
</script>

<style lang="scss">
canvas.chartjs {
  max-width: 100%;
}
</style>
