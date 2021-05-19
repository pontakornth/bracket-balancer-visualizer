<template>
   <h1 class="font-bold text-5xl">Bracket Balancer Visualizer</h1>
   <table class="m-auto w-3/4 my-8 text-5xl">
     <tr>
       <td>Input</td>
       <td>
         <input :disabled="isAnimating" class="border block w-full" type="text" v-model="inputText" />
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
import { defineComponent, ref, reactive } from 'vue'
import HelloWorld from './components/HelloWorld.vue'

type AnimationQueue = {
  stackQueue: string[][],
  imbalancePosition?: number
}

export default defineComponent({
  name: 'App',
  components: {
    HelloWorld
  },
  setup() {
    const isAnimating = ref(false)
    const inputText = ref("")
    const stack = reactive<string[]>([])
    const animationQueue = reactive<AnimationQueue>({stackQueue: []})
    const timeoutQueue = reactive<number[]>([])
    const toggleAnimation = () => {
      isAnimating.value = !isAnimating.value
    }
    const pauseAnimation = () => {
      timeoutQueue.forEach(timeout => clearTimeout(timeout))
    }
    const reset = () => {
      inputText.value = ""
      isAnimating.value = false
      stack.splice(0, stack.length)
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
