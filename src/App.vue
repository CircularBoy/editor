<template>
  <div id="app">
    <Panel msg="Hello world"/>
    <div
      id="edit"
      contenteditable
      @input="listener">
      Change me
<!--      <b>I am bold</b><span>hahah</span>-->
    </div>
    <button @click="selection">getSelection</button>
  </div>
</template>

<script>
import Panel from './components/Panel.vue'

export default {
  name: 'App',
  data: function() {
    return {
      someData: 'Hello kitty',
      allData: []
    }
  },
  mounted: function() {
    this.allData = this.getObjects()
  },
  methods: {
    listener: function(e) {
      this.someData = e.target.innerText
      this.allData = this.getObjects()
      // console.log(this.getObjects())
    },
    selection: function () {
      // console.log(window.getSelection())
      let part = window.getSelection().getRangeAt(0).extractContents()
      console.log(part)
      // let edit = document.getElementById('edit')
      // let range = new Range();
      // range.setStart(edit, 0)
      // range.setEnd(edit, 2)
      // document.getSelection().addRange(range)
    },
    getObjects: function() {
      let edit = document.getElementById('edit');
      let arr = [];

      [].forEach.call(edit.childNodes, function(node) {
        function getStyle(node, styleProp) {
          return node.nodeType === 3 ?
            getComputedStyle(node.parentElement)[styleProp] :
            getComputedStyle(node)[styleProp]
        }

        let obj = {
          text: node.nodeType === 3 ? node.nodeValue : node.innerHTML,
          fontSize: getStyle(node, 'fontSize'),
          color: getStyle(node, 'color')
        }

        arr.push(obj)
      });

      return arr;
    }
  },
  components: {
    Panel
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
