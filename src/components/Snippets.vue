<template>
  <div class="w-full">
    <div v-for="(snippet, index) of snippets" :key="index"
      class="bg-gray-100 w-full rounded p-4 mb-4 text-left">
      <div class="flex items-center">
        <div>
          <div class="text-lg font-bold">{{ snippet.name }}</div>
          <div class="text-sm font-light">{{ snippet.short }}</div>
        </div>
        <div class="ml-auto">
          <button @click="$emit('switch', snippet.code)"
            class="ml-4 px-2 py-1 bg-white border border-blue-500 text-gray-900 rounded text-sm w-16">
            Try
          </button>
          <button v-if="expand.includes(index)" @click="expand = expand.filter(x => x !== index)"
            class="ml-4 px-2 py-1 bg-blue-500 text-white rounded text-sm w-16">
            Hide
          </button>
          <button v-else @click="expand.push(index)"
            class="ml-4 px-2 py-1 bg-blue-500 text-white rounded text-sm w-16">
            View
          </button>
        </div>
      </div>
      <textarea readonly v-model="snippet.code" :rows="snippet.code.split('\n').length"
        class="bg-white text-gray-700 font-mono rounded
          w-full transition-opacity ease-in-out duration-500"
        :class="{
          'border mt-2 p-2 opacity-100': expand.includes(index),
          'opacity-0 block p-0 h-0': !expand.includes(index)
        }"></textarea>
    </div>
  </div>
</template>

<script>
import Snippets from '@/snippets/snippets'

export default {
  name: 'Snippets',

  data() {
    return {
      snippets: Snippets,

      expand: []
    }
  }
}
</script>
