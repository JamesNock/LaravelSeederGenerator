<template>
  <div>
    <div class="overflow-hidden shadow-lg sm:rounded-lg p-6">
      <p class="text-gray-600">
        Enter your INSERT statement...
      </p>
      <form class="my-4">
        <textarea
          v-model="statements"
          required
          class="w-full h-64 border border-solid border-gray-600 p-2"
          name="statements"
          placeholder="INSERT INTO content(`id`, `title`, `text`) VALUES(1, 'title', 'text');"
        />
        <input
          class="block mx-auto p-2
            bg-pink-600 text-white
            border border-solid border-gray-600
            cursor-pointer
            hover:bg-pink-400 hover:text-black"
          type="submit"
          value="Perform the magic!"
          @click.prevent="handleSubmit"
        />
      </form>
      <div v-if="output" class="bg-yellow-300 p-4" @click="copyThis">
        {{ output }}
      </div>
      <div v-if="errorMsg" class="bg-red-500 p-4">
        <span v-html="errorMsg.replace(/(?:\r\n|\r|\n)/g, '<br />')"></span>
      </div>
    </div>
    <div v-if="!fetching" class="max-w-5xl w-full mx-auto mt-8 text-gray-600">
      <div class="overflow-hidden shadow-lg sm:rounded-lg p-6">
        <h4 class="py-4 text-xl font-semibold tracking-wide">How to use</h4>
        <p>
          Copy the file into your seeders folder and then run `<span @click="copyThis" class="bg-pink-300 cursor-pointer">php artisan db:seed</span>`
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useNuxtApp } from '#app'

const statements = ref('')
const output = ref('')
const errorMsg = ref('')
const fetching = ref(false)

const handleSubmit = async () => {
  if (!statements.value) {
    errorMsg.value = "Please enter statements. e.g.\r\nINSERT INTO content(`id`, `title`, `text`)\r\nVALUES\r\n(1, 'title', 'text'),\r\n(2, 'title', 'text');"
    setTimeout(() => {
      errorMsg.value = ''
    }, 5000)
    return
  }

  output.value = 'Fetching...'
  fetching.value = true
  errorMsg.value = ''

  try {
    const formData = new FormData()
    formData.append('statements', statements.value)

    const { data, error } = await useFetch('https://www.james-nock.co.uk/tools/laravelseedergenerator.php', {
      method: 'POST',
      body: formData,
    })

    if (error.value) {
      throw error.value
    }

    if (!data.value.error) {
      // Create blob from response data
      const blob = new Blob([data.value])
      const url = window.URL.createObjectURL(blob)
      const a = document.createElement('a')
      a.href = url
      const filename = statements.value.split(';')[0].split('(')[0].replace(/INSERT\s?INTO\s?/g, '') + 'Seeder.php'
      a.setAttribute('download', filename[0].toUpperCase() + filename.slice(1))
      document.body.appendChild(a)
      a.click()
      output.value = 'File downloading...'
      setTimeout(() => {
        window.URL.revokeObjectURL(url)
        document.body.removeChild(a)
        fetching.value = false
        output.value = ''
      }, 0)
    } else {
      output.value = ''
      errorMsg.value = data.value.message
    }
  } catch (error) {
    errorMsg.value = 'An error occurred while processing your request.'
    output.value = ''
    console.error('Error:', error)
  } finally {
    fetching.value = false
  }
}

const copyThis = (event) => {
  const { $bus } = useNuxtApp()
  $bus.emit('copyThis', event.target)
}
</script>
