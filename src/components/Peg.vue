<template>
  <div :class="isVisible ? null : 'invisible'"
       ref="container"
       id="peg" >
    <div ref="circle"></div>
  </div>
</template>

<script>
export default {
  name: "Peg",
  props: {
    mouseX: Number,
    mouseY: Number,
    width: Number,
    height: Number,
    isVisible: Boolean
  },
  watch: {
    mouseX() {
      this.pegFollowMouse()
    },
    mouseY() {
      this.pegFollowMouse()
    },
    width(newWidth) {
      this.resize(newWidth, this.height)
    },
    height(newHeight) {
      this.resize(this.width, newHeight)
    }
  },
  methods: {
    pegFollowMouse() {
      if (this.container !== undefined) {
        this.container.style.top = `${this.mouseY - this.container.clientHeight / 2}px`
        this.container.style.left = `${this.mouseX - this.container.clientWidth / 2}px`
      }
    },
    resize(width, height) {
      // scales peg to the right size
      this.$refs.circle.style.height = `${this.height}px`;
      this.$refs.circle.style.width = `${this.width}px`;
    }
  },
  data() {
    return {
      container: undefined
    }
  },
  mounted() {
    this.container = this.$refs.container;
  }
}
</script>

<style scoped>
#peg {
  position: fixed;
  pointer-events: none;
}

#peg div {
  background-color: black;
  border-radius: 100%;
}

.invisible {
  visibility: hidden;
}
</style>
