<template>
  <div id="app">
    <div class="control">
      <label class="input_block">
        Введите алфавит:
        <input type="text" v-model="alphabet">
        <div>Если вы укажите пробел сначала или с конца, то он станет частью алфавита</div>
      </label>
      <div class="input_block">
        <label>
          Имя узла:
          <input type="text" v-model="nodeName">
        </label>
        <label>
          Внутренный переход узла:
          <input type="text" v-model="nodeCond">
        </label>
        <button @click="addNode">Добавить узел</button>
      </div>
      <div class="input_block" v-if="editionRelation != -1">
        <label>
          Условие перехода:
          <input type="text" v-model="tmpCond">
        </label>
        <button @click="saveCond">Сохранить условие и закрыть</button>
      </div>
      <div class="input_block">
        <button @click="mode = 'relation'">Добавить переход</button>
      </div>
    </div>
    <div class="columns">
      <Panel :relations="relations" :nodes="nodes" :alphabet="alphabet" :elements="elements"></Panel>
      <div @mouseup="onUp" class="workspace">
        <Relation v-for="r, index in preparedRelations" :index="index" :relation="r"></Relation>
        <Node v-for="n, index in nodes" :node="n" :mode="mode" :index="index"></Node>
      </div>
    </div>
  </div>
</template>
 
<script>

import Panel from './Panel.vue'
import Relation from './Relation.vue'
import Node from './Node.vue'

export default {
  name: 'app',
  components: {
    Panel,
    Relation,
    Node
  },
  data(){return {
    isCreateRelation: false,
    fromRelation: -1,
    toRelation: -1,

    editionRelation: -1,
    tmpCond: '',

    mode: 'default',
    nodeName: '',
    nodeCond: '',
    alphabet: 'a-z @ .',
    nodes: [
      {
        x: 140,
        y: 140,
        name: 'Start',
        cond: '',
        type: 'start'
      },
      {
        x: 50,
        y: 50,
        name: 'Name',
        cond: 'a-z',
        type: ''
      },
      {
        x: 140,
        y: 140,
        name: 'At',
        cond: '',
        type: ''
      },
      {
        x: 200,
        y: 200,
        name: 'Domain',
        cond: 'a-z',
        type: ''
      },
      {
        x: 250,
        y: 250,
        name: 'Dot',
        cond: '.',
        type: ''
      },
      {
        x: 300,
        y: 300,
        name: 'Zone',
        cond: 'a-z',
        type: 'exit'
      }
    ],
    relations: [
      {
        from: 0,
        to:   1,
        cond: 'a-z'
      },
      {
        from: 1,
        to:   2,
        cond: '@'
      },
      {
        from: 2,
        to:   3,
        cond: 'a-z'
      },
      {
        from: 3,
        to:   4,
        cond: '.'
      },
      {
        from: 4,
        to:   5,
        cond: 'a-z'
      }
    ],
    elements: [
      { text: 'dasd@mail.r', status: '' },
      { text: 'dasd@mail.ru', status: 'default' },
      { text: 'a@mail..ru', status: 'default' },
      { text: 'ha', status: 'default' },
      { text: '@mail.ru', status: 'default' }
    ]
  }},
  methods: {
    onUp(e){
      if (this.fromRelation != this.toRelation && this.fromRelation != -1 && this.toRelation != -1){
        this.relations.push({
          from: this.fromRelation,
          to:   this.toRelation
        })
      }
      this.fromRelation = -1
      this.toRelation = -1
      this.mode = 'default'
    },
    addNode(){
      // TODO check on exists name of node
      if (!this.nodeName.length){
        alert('Введите название узла!')
        return false
      }
      this.nodes.push({ x: 10, y: 10, name: this.nodeName, cond: this.nodeCond })
      this.nodeName = ''
      this.nodeCond = ''
    },
    saveCond(){
      this.relations[this.editionRelation].cond = this.tmpCond
      this.editionRelation = -1
    }
  },
  computed: {
    preparedRelations(){
      let newRelations = []
      for (let r of this.relations){
        let start = this.nodes[r.from]
        let end = this.nodes[r.to]
        let left = start.x + start.name.length * 10 / 2
        let top = start.y + 10
        let width = Math.sqrt(Math.pow(start.x - end.x, 2) + Math.pow(start.y - end.y, 2))
        let angle = Math.acos((start.x - end.x) / width) - Math.PI
        if (start.y < end.y) {
          angle = Math.PI - Math.acos((start.x - end.x) / width)
        }
        let transform = angle//"rotate(" +  + "rad)"
        let cond = r.cond
        let type = r.type
        let r2 = {
          top, 
          left, 
          width, 
          transform,
          cond,
          type
        }
        newRelations.push(r2)
      }

      return newRelations
    }
  }
}
</script>