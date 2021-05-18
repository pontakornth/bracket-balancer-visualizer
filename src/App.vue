<template>
   <h1 class="font-bold text-5xl">Bracket Balancer Visualizer</h1>
   <table class="m-auto w-3/4 my-8 text-5xl">
     <tr>
       <td>Input</td>
       <td>
         <div v-if="isAnimating" class="block w-full">
           <span class="inline-block leading-1.15rem" v-for="char, index in inputText" :key="index">{{char}}</span>
         </div>
         <input v-else class="border block w-full" type="text" v-model="inputText" />
        </td>
     </tr>
     <tr>
       <td>Stack</td>
       <td class="text-gray-300">Please animate</td>
     </tr>
   </table>
   <button @click="toggleAnimation" class="button">Animate</button>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import HelloWorld from './components/HelloWorld.vue'

export default defineComponent({
  name: 'App',
  components: {
    HelloWorld
  },
  setup() {
    const isAnimating = ref(false)
    const inputText = ref("")
    const stack = ref<string[]>([])
    const toggleAnimation = () => {
      isAnimating.value = !isAnimating.value
    }
    
    return {
      isAnimating,
      toggleAnimation,
      inputText,
      stack
    }
  }
})
</script>

<style>
#app {
  font-family: "Fira Mono", monospace;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

td {
  @apply text-left p-4;
}

td:first-child {
  @apply text-right;
}

.button {
  @apply text-4xl font-bold bg-blue-500 hover:bg-blue-400 text-white px-4 py-2 rounded-lg;
}

</style>
