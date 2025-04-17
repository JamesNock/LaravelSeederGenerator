<template>
  <div
    class="mt-16"
  >
    <div
      v-if="copiedtoclipboard"
      class="fixed top-0 left-0 w-full p-4 bg-green-400 text-center"
    >
      Copied to clipboard
    </div>
    <div class="max-w-5xl w-full mx-auto sm:px-6 lg:px-8 text-gray-600">
      <div class="overflow-hidden shadow-lg sm:rounded-lg p-6">
        <h1 class="text-3xl font-semibold tracking-wide">
          Laravel Seeder Generator
        </h1>
        <p class="mt-4">
          This is a bog-standard tool for converting an 'INSERT INTO...' statement into a Laravel seeder.<br>Note: Multiple lines after the word VALUES can be used.
        </p>
        <p class="mt-4">
          Paste your statement into the box and hit the button to generate the equivalent seeder.
        </p>
      </div>
      <div class="max-w-5xl w-full mx-auto text-gray-600">
        <div class="mt-6">
          <SeederGenerator />
        </div>
      </div>
      <div class="flex justify-center py-6 space-x-2">
        <a
          href="https://github.com/JamesNock/LaravelSeederGenerator"
          target="_blank"
          ><svg
            class="w-6 h-6 text-gray-600 hover:text-gray-800"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            aria-hidden="true"
            role="img"
            width="32"
            height="32"
            preserveAspectRatio="xMidYMid meet"
            viewBox="0 0 24 24"
          >
            <path
              d="M12 2.247a10 10 0 0 0-3.162 19.487c.5.088.687-.212.687-.475c0-.237-.012-1.025-.012-1.862c-2.513.462-3.163-.613-3.363-1.175a3.636 3.636 0 0 0-1.025-1.413c-.35-.187-.85-.65-.013-.662a2.001 2.001 0 0 1 1.538 1.025a2.137 2.137 0 0 0 2.912.825a2.104 2.104 0 0 1 .638-1.338c-2.225-.25-4.55-1.112-4.55-4.937a3.892 3.892 0 0 1 1.025-2.688a3.594 3.594 0 0 1 .1-2.65s.837-.262 2.75 1.025a9.427 9.427 0 0 1 5 0c1.912-1.3 2.75-1.025 2.75-1.025a3.593 3.593 0 0 1 .1 2.65a3.869 3.869 0 0 1 1.025 2.688c0 3.837-2.338 4.687-4.563 4.937a2.368 2.368 0 0 1 .675 1.85c0 1.338-.012 2.413-.012 2.75c0 .263.187.575.687.475A10.005 10.005 0 0 0 12 2.247z"
              fill="currentColor"
            /></svg
        ></a>
        <a href="https://x.com/JamesNockWebDev" target="_blank"
          >
          <svg
            class="w-6 h-6 text-gray-600 hover:text-gray-800"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            aria-hidden="true"
            role="img"
            width="32"
            height="32"
            preserveAspectRatio="xMidYMid meet"
            viewBox="0 0 24 24"
          >
            <g>
              <path d="M18.244 2.25h3.308l-7.227 8.26 8.502 11.24H16.17l-5.214-6.817L4.99 21.75H1.68l7.73-8.835L1.254 2.25H8.08l4.713 6.231zm-1.161 17.52h1.833L7.084 4.126H5.117z"></path>
            </g>
          </svg>
        </a>
      </div>
        <p class="text-center">By <a class="underline" href="https://www.james-nock.co.uk">James Nock</a></p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useNuxtApp } from '#app'

const copiedtoclipboard = ref(false)

const copyThis = (clickeditem) => {
  if (process.client) {
    const el = document.createElement('textarea')
    el.value = clickeditem.innerText
    el.setAttribute('readonly', '')
    el.style.position = 'absolute'
    el.style.left = '-9999px'
    document.body.appendChild(el)
    const selected =
      document.getSelection().rangeCount > 0
        ? document.getSelection().getRangeAt(0)
        : false
    el.select()
    document.execCommand('copy')
    document.body.removeChild(el)
    if (selected) {
      document.getSelection().removeAllRanges()
      document.getSelection().addRange(selected)
    }
    clickeditem.classList.add('bg-green-400')
    copiedtoclipboard.value = true
    setTimeout(() => {
      clickeditem.classList.remove('bg-green-400')
      copiedtoclipboard.value = false
    }, 3000)
  }
}

onMounted(() => {
  const { $bus } = useNuxtApp()
  $bus.on('copyThis', (clickeditem) => {
    copyThis(clickeditem)
  })
})
</script>
