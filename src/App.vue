<template>
  <div id="app">
    <h>{{count}}</h>
    <br>
    <h>{{double}}</h>
    <br>
    <button @click="increase">+1</button>
    <br>
    <Suspense>
      <template #default>
        <async-show/>
      </template>
      <template #fallback>
        <h1>Loding async show!!!</h1>
      </template>
    </Suspense>
    <h1>X: {{x}},Y: {{y}}</h1>
    <button @click="openModal">Open modal</button>
    <modal :isOpen="modalOpen" @close-model="onModalClose">My Modal OPen!!</modal>
    <span>{{greetings}}</span>
    <h1 v-if="loading">Loading...</h1>
    <img v-if="loaded" :src="result[0].url"/>
    <button @click="updateGreeting">Update Greeting!</button>
    <!-- <ul>
      <li v-for="number in numbers" :key="number"><h1>{{number}}</h1></li>
    </ul>
    <h1>{{person}}</h1> -->
  </div>
</template>

<script lang="ts">
import { computed, ref, reactive, toRefs, watch} from "vue"
import useMousePosition from './hooks/useMousePoints'
import useURLLoader from './hooks/useURLLoader'
import Modal from './components/Modal.vue'
import AsyncShow from './components/AsyncShow.vue'
interface DataPros{
  count: number,
  double: number,
  increase: ()=>void,
  numbers: number[],
  person: {name?: string}
}
interface dogResult{
  message: string;
  success: string;
}
interface catResult{
  id: string;
  url: string;
  width: number;
  height: number;
}
export default{
  name: 'App',
  components: {
    Modal,
    AsyncShow,
  },
  setup() {
    const data:DataPros = reactive({
      count: 0,
      increase: ()=>{
        data.count++
      },
      double: computed(()=>data.count*2),
      numbers: [1,2,3],
      person: {}
    })
    const {result, loading, loaded} = useURLLoader<catResult[]>('https://api.thecatapi.com/v1/images/search?limit=1')
    watch(result,()=>{
      if(result.value){
        console.log('value',result.value[0].url)
      }
    })
    const {x, y} = useMousePosition()
    const greetings = ref('')
    const updateGreeting = () =>{
      greetings.value += 'hello!'
    }
    watch([greetings, ()=>data.count],(newValue, oldValue)=>{
      console.log('old',oldValue)
      console.log('new',newValue)
      document.title = 'updated'+ greetings.value+ data.count
    })
    //完美支持数组和对象的响应式修改
    data.numbers[0]=8
    data.person.name = "yang"
    const refData = toRefs(data)
    const modalOpen = ref(false)
    const openModal = ()=>{
      modalOpen.value = true
    }
    const onModalClose = ()=>{
      modalOpen.value = false
    }
    return {
       ...refData,
       greetings,
       updateGreeting,
       x,
       y,
       result,
       loading,
       loaded,
       modalOpen,
       openModal,
       onModalClose,
     }
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
