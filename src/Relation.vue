<template>
  <div 
    class="relation"
    :style="{top: relation.top + 'px', left: relation.left + 'px', width: relation.width + 'px', transform: 'rotate(' + relation.transform + 'rad)'}"
  >
    <div class="relation_arrow">></div>
    <div class="relation_cond" @click="editRelation" :style="{transform: 'rotate(' + -relation.transform + 'rad)'}">
      {{relation.cond}}
    </div>
  </div>
</template>

<script>
export default {
  data(){return{
    editing: false
  }},
  props: ['relation', 'index'],
  methods:{
    editRelation(){
      this.$parent.editionRelation = this.index
      this.$parent.tmpCond = this.relation.cond
    }
  }
}
</script>
