<template>
  <div 
    ref="el"
    :class="{ node: true, node_start: node.type == 'start', node_exit: node.type == 'exit' }"
    :style="{left: node.x + 'px', top: node.y + 'px'}"
    @mousedown="onDown"
    @mouseup="onUp"
    @mousemove="onMove"
  >
    <b>{{node.name}}</b>
    <span v-if="node.cond.length"> ({{node.cond}})</span>
  </div>
</template>

<script>
export default {
  props: ['node', 'mode', 'index'],
  data(){return {
    selected: false
  }},
  methods: {
    onDown(){
      if (this.mode == 'relation'){
        this.$parent.isCreateRelation = true
        this.$parent.fromRelation = this.index
      }
    },
    onUp(){
      if (this.mode == 'relation'){
        this.$parent.isCreateRelation = false
        this.$parent.toRelation = this.index
      }
      else if (this.mode == 'default'){
        this.node.x = +this.$refs.el.style.left.substring(0, this.$refs.el.style.left.length - 2)
        this.node.y = +this.$refs.el.style.top.substring(0, this.$refs.el.style.top.length - 2)
      }
    },
    onMove(){}
  },
  mounted(){
    if (this.mode == 'default'){
      $(this.$refs.el).draggable();
    }
    console.log(this.node)
  },
  watch:{
    mode: function(now){
      if (now == 'relation'){
        $(this.$refs.el).draggable("disable");
      }
      else if (now == 'default'){
        $(this.$refs.el).draggable("enable");
      }
    }
  }
}
</script>
