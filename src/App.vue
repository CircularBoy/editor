<template>
  <div id="app">
    <Panel msg="Hello world"/>
    <button @click="changeSize('fontSize', '20px')">Size</button>
    <button @click="changeSize('color', 'green')">Color</button>
    <div
      id="edit"
      contenteditable
      @input="listener">
      Change me to some another
<!--      <span style="font-size: 18px; color: #ebebeb;">I am bold</span><span style="font-size: 14px; color: #000">hahah</span>-->
    </div>
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
    changeSize: function(
      // styleName, styleValue
    ) {
      let selection = window.getSelection();
      // let range = selection.getRangeAt(0).surroundContents(document.createElement('span'))
      let range = selection.getRangeAt(0);

      console.dir(selection)
      console.dir(range)
      // let parseTags = function(range) {
      //   let newRange = range.cloneNode(true);
      //   let collectionElem = document.createDocumentFragment();
      //   for(let i = 0; i < newRange.childNodes.length; i++) {
      //     let node = newRange.childNodes[i]
      //     // collectionElem.appendChild(node)
      //     if(node.nodeType === 3) {
      //       let modifyNode = document.createElement('span')
      //       modifyNode.innerHTML = node.nodeValue;
      //       modifyNode.style[styleName] = styleValue;
      //       console.log(modifyNode)
      //       collectionElem.appendChild(modifyNode)
      //     } else {
      //       node.style[styleName] = styleValue;
      //       collectionElem.appendChild(node.cloneNode(true))
      //     }
      //   }
        // let arr = [];
        // range.childNodes.forEach(function(node) {
        //   console.dir(node)
        //   if(node.nodeType === 3) {
        //     let modifyNode = document.createElement('span')
        //     modifyNode.innerHTML = node.nodeValue
        //     arr.push(modifyNode)
        //   } else {
        //     arr.push(node)
        //   }
        // })
        // window.getSelection().empty()
      //   return collectionElem;
      // };
      // let cloneRange = range.extractContents();
      // console.log(cloneRange)
      // let arr = parseTags(cloneRange)
      // range.insertNode(arr)
      //parse tags if selection had tags inside
      // (function (range) {
      //   let arr = [range.startContainer.parentNode,range.endContainer.parentNode];
      //   arr.forEach((elem) => {
      //     if(elem !== document.getElementById('edit')) {
      //       parseTags(range.extractContents())
      //     }
      //   })
      // }(range));

    },
    selection: function () {

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
