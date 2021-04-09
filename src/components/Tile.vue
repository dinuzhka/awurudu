<template>
  <div @click="selectThis" class="img-tile" :class="{'selected-border': tile.selected}" :style="{'background-position-x': positionX, 'background-position-y': positionY, margin: (tileMarginPx+'px'), 'background-color': backColor}">
    <span v-if="showNumber">{{tile.id}}</span>
  </div>
</template>
<script>
export default {
  name: 'Tile',
  props: {
    tile: {
      type: Object,
      require: true
    },
    rowNumber: {
      type: Number,
      require: true
    },
    showNumber: {
      type: Boolean,
      default: true
    },
    columnNumber: {
      type: Number,
      require: true
    },
    rowCount: {
      type: Number,
      require: true
    },
    columnCount: {
      type: Number,
      require: true
    },
    tileHeight: {
      type: Number,
      require: true
    },
    tileWidth: {
      type: Number,
      require: true
    },
    tileMarginPx: {
      type: Number,
      require: true
    },
    colors: {
      type: Array,
      require: true
    },
    selectionExceeded: {
      type: Boolean,
      default: false
    },
    isGameRunning: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    positionX: function () {
      return ((this.columnNumber - 1) * this.columnCount ) + '%'
    },
    positionY: function () {
      return ((this.rowNumber - 1) * this.rowCount ) + '%'
    },
    backColor: function () {
      if(this.tile.selected){
        return '#ffffff'  
      }
      let min = 0, max = this.colors.length -1
      let randomIndex = Math.floor(Math.random() * (max - min + 1)) + min
      return this.colors[randomIndex]
    }
  },
  methods: {
    selectThis: function () {
      if(this.isGameRunning) {
        let selected = this.tile.selected
        if(selected) {
          this.$set(this.tile, 'selected', false)
        }else{
          if(!this.selectionExceeded){
            this.$set(this.tile, 'selected', true)
          }
        }
      }
    }
  }
}
</script>
<style scoped>
.img-tile{
  float: left;
}
.selected-border {
  border: 2px solid red;
  margin: 0 !important;
}
</style>