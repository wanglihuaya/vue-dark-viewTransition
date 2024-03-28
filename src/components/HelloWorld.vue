<script
  setup
  lang="ts"
>
import { nextTick, ref } from 'vue';


defineProps<{ msg: string }>()

const count = ref(0)
const isDark = ref(false)
console.log('isDark', isDark.value)


const enableTransitions = () =>
  'startViewTransition' in document &&
  window.matchMedia('(prefers-reduced-motion: no-preference)').matches

const handleClick = async ({ clientX: x, clientY: y }: MouseEvent) => {
  const html = document.querySelector('html')
  if (isDark.value) {
    html?.classList.add('dark')
    html?.classList.remove('light')
  } else {
    html?.classList.remove('dark')
    html?.classList.add('light')
  }

  if (!enableTransitions()) {
    isDark.value = !isDark.value
    return
  }

  const clipPath = [
    `circle(0px at ${x}px ${y}px)`,
    `circle(${Math.hypot(
      Math.max(x, innerWidth - x),
      Math.max(y, innerHeight - y)
    )}px at ${x}px ${y}px)`
  ]

  await document.startViewTransition(async () => {
    isDark.value = !isDark.value
    await nextTick()
  }).ready

  document.documentElement.animate(
    { clipPath: isDark.value ? clipPath.reverse() : clipPath },
    {
      duration: 300,
      easing: 'ease-in',
      pseudoElement: `::view-transition-${isDark.value ? 'old' : 'new'}(root)`
    }
  )


}

</script>

<template>
  <h1>{{ msg }}</h1>
  <div class="card">
    <button
      type="button"
      @click="count++"
    >count is {{ count }}</button>
    <t-icon
      name="mode-dark"
      class="dark-icon"
      @click="handleClick"
    ></t-icon>

  </div>

  <p>
    Check out
    <a
      href="https://vuejs.org/guide/quick-start.html#local"
      target="_blank"
    >create-vue</a>, the official Vue + Vite starter
  </p>
  <p>
    Install
    <a
      href="https://github.com/vuejs/language-tools"
      target="_blank"
    >Volar</a>
    in your IDE for a better DX
  </p>
  <p class="read-the-docs">Click on the Vite and Vue logos to learn more</p>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}

.dark-icon {
  width: 24px;
  height: 24px;
  margin-left: 10px;
  background-color: #1a1a1a;
  border-radius: 50%;
  padding: 10px;
  border: 1px solid transparent;
}

.dark-icon:hover {
  background-color: #333;
  border-color: #646cff
}

.dark-icon:active {
  background-color: #444;
}

::view-transition-old(root),
::view-transition-new(root) {
  animation: none;
  mix-blend-mode: normal;
}

::view-transition-old(root),
.dark::view-transition-new(root) {
  z-index: 1;
}

::view-transition-new(root),
.dark::view-transition-old(root) {
  z-index: 9999;
}
</style>
