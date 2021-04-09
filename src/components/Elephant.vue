<template>
  <div class="elephant-container inline-block" :style="{width: (tileRowWidth + 'px') }">
    <div class="tile-container inline-block" :style="{width: (tileRowWidth + 'px') }">
      <div v-for="row in displayRows" :key="row.id" class="tile-row" :style="{width: (tileRowWidth + 'px'), height: tileHeightPx }">
        <Tile v-for="tile in row.tiles" :key="tile.id" :rowNumber="tile.row" :columnNumber="tile.col" :tileWidth="tileWidth" :tileHeight="tileHeight"
        :tileMarginPx="tileMarginPx" :tile="tile" :showNumber="!isRunning" :colors="colors" :selectionExceeded="selectionExceeded"
        :rowCount="tileRows" :columnCount="tileColumns" :style="{width: tileWidthPx, height: tileHeightPx, backgroundImage: `url('${displayImageUrl}')`}"
        :isGameRunning="isRunning"
        />
      </div>
    </div>
    <div class="action-pane">
      <h4>Let the game</h4>
      <button v-if="!isRunning" @click="start">Start</button>
      <button v-if="isRunning" @click="reveal">Reveal</button>
    </div>
  </div>
</template>
<script>
import Tile from './Tile'
import _ from 'lodash'
export default {
  name: 'Elephant',
  components: {
    Tile
  },
  data () {
    return {
      isRunning: false,
      showImageTimer: 0,
      imageVisibilityMS: 1000,
      intervalMS:100,
      imageUrl: 'https://images.fineartamerica.com/images-medium-large-5/side-view-of-elephant-amboseli-julia-cumes.jpg',
      imageWidth: 0,
      imageHeight: 0,
      tileColumns: 10,
      tileRows: 10,
      tileMarginPx: 2,
      tiles: [],
      interval: undefined,
      colors: [
        '#ff2472', '#9e4c69', '#884c9e','#544bb3', '#1ec3e8','#2fad85', '#00d936','#426e38','#9fc710',
        '#ffb114','#c45d25','#d92616'
      ]
    }
  },
  computed: {
    selectionExceeded: function () {
      let selection = _.filter(this.tiles, function(t){return t.selected})
      return selection.length >= 3
    },
    tileRowWidth: function () {
      return this.imageWidth + ((this.tileColumns * 2) * this.tileMarginPx)
    },
    remainingTimeForShowing: function () {
      return (this.imageVisibilityMS - this.showImageTimer) + ' milliseconds'
    },
    tileWidth: function () {
      return (this.imageWidth / this.tileColumns)
    },
    tileHeight: function () {
      return (this.imageHeight / this.tileRows)
    },
    tileWidthPx: function () {
      return this.tileWidth + 'px'
    },
    tileHeightPx: function () {
      return this.tileHeight + 'px'
    },
    displayRows: function () {
      let rows = []
      let rowid = 1
      let row = {
        id: rowid,
        tiles: []
      }
      let sortedTiles = _.orderBy(this.tiles, function (s) {return s.displayorder} )
      for(let r=0;r<sortedTiles.length;r++){
        if(r > 0 && r % this.tileRows === 0) {
          rows.push(row)
          rowid++
          row = {
            id: rowid,
            tiles: []
          }
        }
        let t = sortedTiles[r]
        row.tiles.push(t)
      }
      if(row.tiles.length > 0) {
        rows.push(row)
      }
      return rows
    },
    displayImageUrl: function () {
      if(this.isRunning) {
        return ''
      } else {
        return this.imageUrl
      }
    }
  },
  methods: {
    reveal: function () {
      var person = prompt("Please enter your name", "");
      if(person!=null){
        clearInterval(this.interval)
        for(let u=0;u<this.tiles.length;u++){
          this.$set(this.tiles[u], 'displayorder', this.tiles[u].id)
        }
        this.isRunning = false
      }
    },
    start: function () {
      for(let u=0;u<this.tiles.length;u++){
        this.$set(this.tiles[u], 'displayorder', this.tiles[u].id)
        this.$set(this.tiles[u], 'selected', false)
      }
      this.shuffleTiles()
      this.isRunning = true
      
      this.interval = setInterval(()=>{
          //clearInterval(i)
          //this.isRunning = false
          this.shuffleTiles()
      }, 5000)

      /*var i = setInterval(()=>{
        this.showImageTimer += this.intervalMS
        if(this.showImageTimer > this.imageVisibilityMS){
          clearInterval(i)
          this.isRunning = false
        }
      }, this.intervalMS)*/
    },
    shuffle: function (array) {
       var currentIndex = array.length, temporaryValue, randomIndex;
      // While there remain elements to shuffle...
      while (0 !== currentIndex) {
        // Pick a remaining element...
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex -= 1;

        // And swap it with the current element.
        temporaryValue = array[currentIndex];
        array[currentIndex] = array[randomIndex];
        array[randomIndex] = temporaryValue;
      }
      return array;
    },
    shuffleTiles: function () {
      this.tiles = this.shuffle(this.tiles)
      for(let u=0;u<this.tiles.length;u++){
        this.$set(this.tiles[u], 'displayorder', u)
      }
    },
    calculateAspects: function () {
      let image = new Image();
      image.src = this.imageUrl
      let self = this
      image.onload = function () {
        self.imageWidth = this.naturalWidth
        self.imageHeight = this.naturalHeight
      }
    },
    generateTiles: function () {
      let tileid=1;
      for(let r=0;r<this.tileRows;r++) {
       for(let c=0;c<this.tileColumns;c++) {
         this.tiles.push({
           id: tileid,
           row: (r + 1),
           col: (c + 1),
           displayorder: tileid
         })
         tileid++
        } 
      }
    }
  },
  mounted: function () {
    this.generateTiles()
    this.calculateAspects()
  }
}
</script>
<style scoped>
.tile-row {
  display: block;
}
.elephant-container{
  margin:0 auto;
}
.inline-block{
  display: inline-block;
}
.action-pane {
    position: absolute;
    left: 10px;
    top: 10px;
}
</style>