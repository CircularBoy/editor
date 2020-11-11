<template>
  <div id="app">
<!--    <Panel msg="Hello world"/>-->
    <button @click="changeSize('fontSize', '20px')">Size</button>
    <button @click="changeSize('color', 'green')">Color</button>
    <div
      id="edit"
      contenteditable
      @input="listener">
      Change me to some another
      <span></span>
      <span style="font-size: 18px; color: #ebebeb;">I am bold</span>
<!--      <span style="font-size: 18px; color: #ebebeb;">I am bold</span><span style="font-size: 14px; color: #000">hahah</span>-->
<!--      <span style="font-size: 18px; color: #ebebeb;">I am bold</span><span style="font-size: 14px; color: #000">hahah</span>-->
    </div>
  </div>
</template>

<script>
// import Panel from './components/Panel.vue'

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
      styleName, styleValue
    ) {

      //start here
      //get selection
      let selection = window.getSelection();
      //get child of selection for find siblings elements of selection
      // let firstChild = selection.anchorNode.parentNode;
      // let lastChild = selection.focusNode.parentNode;
      //get siblings elements for compare styles with selection
      // let prevSibling = getPrevSibling(selection)
      // let prevSibling = selection.anchorNode.parentNode.previousSibling
      // let nextSibling = selection.focusNode.nextSibling

      //log all variable

      // get range of selection
      let range = selection.getRangeAt(0);
      // extract content of range from the document
      let inner = range.extractContents()
      //create new document fragment contains child spans
      let elements = iterateRange(inner)
      //merge all span with samilar styles
      let selectionWithoutCopy = compareStyleSelectionNodes(elements);
      //check if siblings of selection contains samilar styles, merge it
      // compareStyleSiblings(selectionWithoutCopy, prevSibling, nextSibling)
      //create nodelist from array
      let nodeListOfElements = createNodeListFromArray(selectionWithoutCopy)

      //insert our pretty nodelist into editor
      // insertIntoEditor(nodeListOfElements, prevSibling)
      // console.log(nodeListOfElements)
      range.insertNode(nodeListOfElements)
      parseEditor(document.querySelector('#edit'))

      function iterateRange(range) {
        //copy range for continua work with original range
        let newRange = range.cloneNode(true);
        //create fragment for append node to one level tree
        let fragment = document.createDocumentFragment();
        let child;

        //append child before all node will be append to new fragment
        while ((child = newRange.firstChild)) {
          //check if nodeType text node - wrap it to <span>
          if (child.nodeType === 3) {
            let wrap = document.createElement('span')
            //insert data to span
            wrap.appendChild(child);
            child = wrap;
          }
          child.style[styleName] = styleValue
          fragment.appendChild(child)
        }
        return fragment;
      }

      function compareStyles(nodeOne, nodeTwo) {
        let stylesOfOne = nodeOne.getAttribute('style').slice(0, -1).split(';')
        let stylesOfTwo = nodeTwo.getAttribute('style').slice(0, -1).split(';')
        if(stylesOfOne.length === stylesOfTwo.length) {
          for(let i=0; i<stylesOfOne.length; i++) {
            if(stylesOfOne[i] !== stylesOfTwo[i]) {
              return false
            }
          }
        } else {
          return false
        }

        return true
      }

      function mergeNodes(node, insertNode) {
        let text = insertNode.textContent
        node.textContent += text;
        return node;
      }

      function compareStyleSelectionNodes(collection) {
        //create empty arr
        let arrNodes = [];
        //iterate our collection
        for(let i=0, prevElem; i < collection.childNodes.length; i++) {
          //create shorcut for collection children
          let node = collection.childNodes[i];
          //if prevElement has some node we can compare it with current node
          if(prevElem) {
            //get results of compare two node
            let isEqual = compareStyles(node, prevElem)
            //if node styles equal merge them
            if(isEqual) {
              //put results of merge into prev element of array
              arrNodes[i-1] = mergeNodes(prevElem,node);
            } else {
              //if styles different simple push node to array
              arrNodes.push(node)
            }
          } else {
            //if prevElement undefined simple push our node to arr
            arrNodes.push(node)
          }
          //put node to prevElement for next iteration
          prevElem = node
        }
        return arrNodes;
      }

      // function compareStyleSiblings(arr, prevSibling, nextSibling) {
      //   let firstChild = arr[0];
      //   let lastChild = arr[arr.length - 1];
      //
      //   if(prevSibling) {
      //     // let prev = compareStyles(firstChild, prevSibling)
      //     // console.log(prev)
      //     // if(prev)
      //       // mergeNodes(prevSibling, firstChild)
      //   }
      //
      //   if(nextSibling) {
      //     // let next = compareStyles(lastChild, nextSibling)
      //     // console.log(next)
      //     // if(next)
      //       // mergeNodes(nextSibling, lastChild)
      //   }
      //
      //   console.log(firstChild)
      //   console.log(lastChild)
      //   console.log(arr)
      // }

      function createNodeListFromArray(arr) {
        let fragment = document.createDocumentFragment()
        for(let i=0; i < arr.length; i++) {
          fragment.appendChild(arr[i])
        }

        return fragment
      }

      function parseEditor(elem) {


        parseTree(elem.childNodes, elem);

        //create obj with styles
        function strStylesToObj(node) {
          let arr = node.getAttribute('style').slice(0, -1).split(';')
          let styleObjArr = []
          arr.forEach(function(style) {
            let styleArr = style.split(':')
            let obj = {}
            obj[styleArr[0]] = styleArr[1]
            styleObjArr.push(obj)

          })

          return styleObjArr;
        }

        function parseStyle(list, parent) {
          for(let i=0;i < list.length;i++) {
            let node = list[i];
            if(node.nodeType !== 3) {
              let stylesParent = strStylesToObj(parent)
              let stylesNode = strStylesToObj(node)

              console.log(stylesParent)
              console.log(stylesNode)

              for(let nodeS in stylesNode) {
                console.log(nodeS)
              }
            }
          }
        }

        function parseTree(list, parent) {
          for(let i=0; i < list.length; i++) {
            let node = list[i];
            if(node.nodeType === 3) {
              if(!node.nodeValue) {
                parent.removeChild(node)
                return
              }
            } else {
              if(!node.textContent) {
                parent.removeChild(node)
              }
            }
            if(node.childNodes) {
              parseTree(node.childNodes, node)

              parseStyle(node.childNodes, node)
            }

          }
        }
      }
      //
      // function insertIntoEditor(nodeList, prevSibling) {
      //
      //   if(prevSibling) {
      //     prevSibling.after(nodeList)
      //   }
      // }

    },
    getObjects: function() {
      let edit = document.getElementById('edit');
      let arr = [];

      [].forEach.call(edit.childNodes, function (node) {
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
    // Panel
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
