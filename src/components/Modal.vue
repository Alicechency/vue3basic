<template>
<teleport to="#modal">
  <div id="center" v-if="isOpen">
    <h2><slot>this is a modal</slot></h2>
    <button @click="buttonClick">close</button>
  </div>
</teleport>
</template>
<script lang="ts">
import { defineComponent } from 'vue'
export default defineComponent({
    props:{
        isOpen: Boolean
    },
    emits:{
        // 'close-model': (payload : any)=>{
        //     //验证函数，可以设置成null
        //     return payload.type === 'closed'
        // }
        'close-model': null
    },
    setup(props,context){
        const buttonClick = ()=>{
            // context.emit('close-model',{
            //     type:'hello'
            // })
            context.emit('close-model')
        }
        return{
            buttonClick
        }
    }
})
</script>
<style scoped>
  #center {
    width: 200px;
    height: 200px;
    border: 2px solid black;
    background: white;
    position: fixed;
    left: 50%;
    top: 50%;
    margin-left: -100px;
    margin-top: -100px;
  }
</style>
