<template>
  <div>
<!--    <button @click="incFont">Increase font</button>-->
<!--    <button @click="decFont">Decrease font</button>-->
    <p>
      <button @click="desc">-</button>
      <span>Change font({{currentSize}})</span>
      <button @click="inc">+</button>
    </p>
  </div>
</template>

<script>
export default {
  name: 'Panel',
  props: {
    edit: String
  },
  data: function() {
    return {
      currentSize: 3
    }
  },
  methods: {
    inc: function() {
      this.currentSize = this.currentSize <= 7 ? this.currentSize += 1 : 7;
      document.execCommand('fontSize', false, this.currentSize)
    },
    desc: function() {
      this.currentSize = this.currentSize >= 1 ? this.currentSize -= 1 : 1;
      document.execCommand('fontSize', false, this.currentSize)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
