<template lang="html">
  <div class="" ref="slider">
    <span class="pos-abs slider-tips tx-c m-t-20"
          v-for="(i,index) in data"
          :key="i">
          {{data[index].value}}%
          <br>
          {{data[index].label}}
    </span>
  </div>
</template>

<script>
import noUiSlider from 'noUiSlider'
import 'noUiSlider/distribute/noUiSlider.min.css'

export default {
  props: {
    data: {
      type: Array,
      default() {
        return []
      }
    },
    colors: {
      type: Array,
      default() {
        return []
      }
    }
  },
  data() {
    return {
      _data: []
    }
  },
  watch: {
    data(newValue) {
      this._data = this.convert(newValue.map(item => item.value))
    }
  },
  created() {
    this._data = this.convert(this.data.map(item => item.value))
  },
  mounted() {
    this.$nextTick(() => {
      var slider = this.$refs.slider
      this.slider = slider
      noUiSlider.create(slider, {
        start: this._data,
        connect: this.data.map(item => true),
        tooltips: false,
        behaviour: 'none',
        step: 1,
        range: {
          'min': [ 0 ],
          'max': [ 100 ]
        }
      })
      var connect = slider.querySelectorAll('.noUi-connect')
      var origin = slider.querySelectorAll('.noUi-origin')
      var tips = slider.querySelectorAll('.slider-tips')
      var classes = ['c-1-color', 'c-2-color', 'c-3-color', 'c-4-color']
      for (var i = 0; i < connect.length; i++) {
        connect[i].classList.add(classes[i])
        tips[i].style.left = connect[i].style.left
        tips[i].style.right = connect[i].style.right
      }
      origin.forEach((item, index) => {
        if (this.data[index].disabled === true || this.data[index].disabled === false) {
          item.setAttribute('disabled', this.data[index].disabled)
          connect[index].style.background = '#ccc'
        }
      })
      slider.noUiSlider.on('end', () => {
        this.resetLocation()
      })
      slider.noUiSlider.on('slide', () => {
        this.resetLocation()
      })
      setTimeout(() => {
        this.resetLocation()
      }, 20)
    })
  },
  methods: {
    convert(data) {
      const length = data.length
      let sum = 0
      let arr = []
      data.forEach((item, index) => {
        if (index === length - 1) {
          return
        }
        sum += item
        arr.push(sum)
      })
      return arr
    },
    reverse(data) {
      let sum = 0
      let arr = []
      data.forEach((item, index) => {
        arr.push(item - (data[index - 1] !== undefined ? parseInt(data[index - 1]) : 0))
      })
      arr.push(100 - _.sum(arr))
      return arr
    },
    resetLocation() {
      var connect = this.slider.querySelectorAll('.noUi-connect')
      var tips = this.slider.querySelectorAll('.slider-tips')
      for (var i = 0; i < connect.length; i++) {
        tips[i].style.left = connect[i].style.left
        tips[i].style.right = connect[i].style.right
      }
      this._data = this.reverse(this.slider.noUiSlider.get())
      this.data.forEach((item, index) => {
        item.value = this._data[index]
      })
    }
  }
}
</script>

<style lang="css" scoped>
.slider-tips {
  min-width: 60px;
  line-height: 18px;
}
</style>
