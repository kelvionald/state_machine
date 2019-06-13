<template>
  <div class="test_panel">
    <button @click="test">Test</button>
    <input :class="elements[k].status" type="text" v-for="i, k in elements" :key="'_case_' + k" v-model="elements[k].text">
    <button @click="addElement">+</button>
  </div>
</template>

<script>
export default {
  name: 'Panel',
  props: ['relations', 'nodes', 'alphabet', 'elements'],
  methods:{
    addElement(){
      this.elements.push({
        text: '',
        status: 'default'
      })
    },
    getEnumation(el){ //pattern
      let extendedAb = []
      if (el.length == 3){
        if (el[1] == '-'){
          let from = el[0].charCodeAt(0)
          let to = el[2].charCodeAt(0)
          if (from < to){
            for (; from <= to; from++){
              extendedAb.push(String.fromCharCode(from))
            }
          }
        }
        else{
          alert('Ошибка алфавита!')
        }
      }
      else if (el.length == 1){
        extendedAb.push(el)
      }
      else if (el.length == 0){
        extendedAb.push(' ')
      }
      else{
        alert('invalid alphabet')
      }
      return extendedAb
    },
    test(){
      // Парсим алфавит
      let parsedAb = this.alphabet.split(' ')
      let extendedAb = []
      for (let el of parsedAb){
        // enumeration
        let tmp = this.getEnumation(el)
        tmp.map(function(val, i){
          extendedAb.push(val)
        })
      }
      console.log(extendedAb)
      let localNodes = JSON.parse(JSON.stringify(this.nodes))
      let localRelations = JSON.parse(JSON.stringify(this.relations))
      // console.log(localNodes)
      for (let n of localNodes){
        n.extended = this.getEnumation(n.cond)
      }
      for (let r of localRelations){
        r.extended = this.getEnumation(r.cond)
      }
      console.log(localRelations)
      for (let el of this.elements){
        let str = Array.from(el.text)
        let isError = false
        let isStart = true
        let currentState = 'Start'
        let indexOfState = -1
        for (let ch of str){
          console.log(currentState, ch)
          let isLoop = false
          let isJump = false
          for (let j in localNodes){
            let n = localNodes[j]
            if (isStart){
              if (n.type == 'start'){
                isStart = false
                currentState = n.name
                indexOfState = j
                if (n.extended.includes(ch)){
                  isLoop = true
                }
                break
              }
            }
            else{
              if (n.name == currentState){
                indexOfState = j
                if (n.extended.includes(ch)){
                  isLoop = true
                }
                break
              }
            }
          }
          if (!isLoop){
            for (let i in localRelations){
              let r = localRelations[i]
              if (r.from == indexOfState){
                if (r.extended.includes(ch)){
                  currentState = localNodes[r.to].name
                  indexOfState = r.to
                  isJump = true
                  break
                }
              }
            }
            isError = !isJump
            if (isError){
              console.error('error')
              el.status = 'case_error'
              break
            }
          }
        }
        if (!isError){
          if (indexOfState == -1)
          {
            console.error('wtf')
          }
          if (localNodes[indexOfState].type == 'exit'){
            console.log('SUCCESS')
            el.status = 'case_success'
          }
          else{
            console.error('Unexpected end of line')
            el.status = 'case_error'
          }
        }
      }
    }
  },
  mounted(){
    // this.test()
  }
}
</script>