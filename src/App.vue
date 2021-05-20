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
       <td v-if="!isAnimating" class="text-gray-300">Please animate</td>
       <td v-else>{{displayStack.value.join(" ")}}</td>
     </tr>
   </table>
   <div class="button-group mx-auto">
    <button @click="onClickAnimateButton" class="button">{{animateButtonText}}</button>
    <button @click="reset" class="button bg-red-500 hover:bg-red-400" v-if="isAnimating || !isAnimationDone">Reset</button>
   </div>
   <p v-if="isAnimating && animationQueue.imbalancePosition != -1">The imbalance is at character {{animationQueue.imbalancePosition}}.</p>
</template>

<script lang="ts">
import { defineComponent, ref, reactive, computed } from 'vue'
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
    const isAnimationDone = ref(true)
    const inputText = ref("")
    const displayStack = reactive<{value: string[]}>({value: []})
    const animationQueue = reactive<AnimationQueue>({stackQueue: [], imbalancePosition: -1})
    const timeoutQueue = reactive<number[]>([])
    const animateButtonText = computed(() => {
      return isAnimating.value ? 'Pause' : 'Animate'
    })
    const onClickAnimateButton = computed(() => {
      if (isAnimating.value) {
        console.log("Pause animation")
        return pauseAnimation
      } else if (isAnimationDone.value) {
        console.log("Start Animation")
        return startAnimation
      } else {
        console.log("Resume Animation")
        return animate
      }
    })
    const toggleAnimation = () => {
      isAnimating.value = !isAnimating.value
    }
    const pauseAnimation = () => {
      timeoutQueue.forEach(timeout => clearTimeout(timeout))
      timeoutQueue.splice(0, timeoutQueue.length)
      isAnimating.value = false
    }
    const reset = () => {
      inputText.value = ""
      isAnimating.value = false
      displayStack.value = []
      isAnimationDone.value = true
      pauseAnimation()
      animationQueue.stackQueue = []
    }

    const checkBalance = () => {
      const opening = ["(", "{", "["]
      const closing = [")", "}", "]"]
      const matching = ["()", "{}", "[]"]
      const stack: string[] = []
      for (let index in [...inputText.value]) {
        const char = inputText.value[index]
        if (opening.includes(char)) {
          stack.push(char)
          animationQueue.stackQueue.push([...stack])
        } else if (closing.includes(char)) {
          if (matching.includes(stack[stack.length - 1] + char)) {
            stack.pop()
            animationQueue.stackQueue.push([...stack])
          } else {
            // Bracket does not match
            animationQueue.imbalancePosition = parseInt(index) + 1
            return
          }
        }
      }
      if (stack.length > 0) {
        animationQueue.imbalancePosition = inputText.value.length
      } else {
        animationQueue.imbalancePosition = -1
      }
    }


    const animate = () => {
      animationQueue.stackQueue.forEach((stack, index) => {
        console.log("Set timeout ")
        const timeout = setTimeout(() => {
          displayStack.value = [...stack]
          animationQueue.stackQueue.splice(0, 1)
        }, (index + 1) * 200)
        timeoutQueue.push(timeout)
      })
      timeoutQueue.push(setTimeout(() => {
        isAnimating.value = false
        isAnimationDone.value = true
      }, animationQueue.stackQueue.length * 200))
    }

    const startAnimation = () => {
      isAnimating.value = true
      isAnimationDone.value = false
      animationQueue.stackQueue = []
      displayStack.value = []
      checkBalance() // This function enqueue the animatino process.
      animate()
    }
    
    return {
      isAnimating,
      toggleAnimation,
      inputText,
      displayStack,
      animate,
      animationQueue,
      timeoutQueue,
      startAnimation,
      animateButtonText,
      onClickAnimateButton,
      isAnimationDone,
      reset
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

.button-group {
  @apply flex justify-center;
  * {
    @apply mr-4;
  }
}


</style>
