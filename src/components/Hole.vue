<template>
  <div @mousedown="hasPeg ? $emit('pegGrabbed', row, column) : log('No peg to grab.')"
       @mouseover="isHovered = true"
       @mouseout="isHovered = false"
       ref="hole"
       id="hole">
    <div :class="hasPeg ? null : 'hidden'"
         id="peg">
    </div>
  </div>
</template>

<script>
export default {
  name: "Hole",
  data() {
    return {
      isHovered: false
    }
  },
  props: {
    row: Number,
    column: Number,
    hasPeg: Boolean,
    color: String
  },
  emits: ['pegGrabbed', 'holeHovered'],
  watch: {
    isHovered(newValue) {
      // being hovered
      if (newValue) {
        this.$emit('holeHovered', this.row, this.column);

        this.color === 'black' ?
          this.setBorder(this.$refs.hole, this.color, '1px') :
          this.setBorder(this.$refs.hole, this.color, '2px')
      } else {
        this.setBorder(this.$refs.hole, 'black', '1px')
      }
    }
  },
  methods: {
    setBorder(htmlElement, color = 'black', width = '1px', style = 'solid') {
      htmlElement.style.border = `${color} ${width} ${style}`
    },
    log(msg) {
      console.log(msg)
    }
  }
}
</script>

<style scoped>
#hole {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 90%;
  height: 90%;
  border: black 1px solid;
  border-radius: 100%;
  box-sizing: border-box;
  user-select: none;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
}

#peg {
  width: 80%;
  height: 80%;
  background-color: black;
  border-radius: 100%;
}

.hidden {
  visibility: hidden;
}
</style>
