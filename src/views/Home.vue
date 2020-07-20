<template>
  <div>
    <!-- Heading -->
    <div class="text-center mt-8">
      <div class="tracking-widest text-4xl font-bold">
        PAGENT
      </div>
      <div class="text-xl font-light text-gray-700">
        DEMO
      </div>
    </div>

    <!-- Body -->
    <div class="max-w-4xl mx-auto flex flex-wrap mt-8 px-8">
      <div class="w-full md:w-1/2 md:pr-3">
        <div class="flex items-center mb-2">
          <div class="text-2xl" id="source-input">Source</div>

          <div class="ml-auto flex items-center">
            <div class="flex items-center">
              <label for="auto-compile" class="pr-2 text-gray-700 font-light">Auto</label>
              <input type="checkbox" id="auto-compile" class="w-4 h-4"
                v-model="autoCompile" @input="checkCompile(!autoCompile)">
            </div>

            <button @click="compile" v-if="!autoCompile"
              class="ml-4 px-2 py-1 bg-blue-500 text-white rounded text-sm">
              Compile
            </button>
          </div>
        </div>
        <textarea ref="code" rows="20" v-model="source"
          @keydown.tab.prevent="insertTab" @input="checkCompile(autoCompile)"
          class="border rounded-lg w-full font-mono p-2"></textarea>
      </div>
      <div class="w-full md:w-1/2 md:pl-3">
        <div class="text-2xl mb-2">Content</div>
        <div v-if="error" class="bg-red-200 rounded p-2 text-red-900 font-mono">
          <div class="font-bold text-center mb-2">ERROR</div>

          <div class="whitespace-pre-line">
            {{ error }}
          </div>
        </div>
        <div v-else id="content" class="bg-gray-100 rounded p-2"></div>
        <div class="text-center text-4xl text-gray-400">...</div>
      </div>
      <div class="w-full">
        <div class="text-2xl mb-2 mt-4">Snippets</div>
        <Snippets @switch="switchSource"/>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

import HelloWorld from '@/snippets/hello-world.txt'

import Snippets from '@/components/Snippets'

export default {
  name: 'Home',

  components: { Snippets },

  data() {
    return {
      error: null,

      source: HelloWorld,

      autoCompile: true,
    }
  },

  mounted() {
    this.compile()
  },

  methods: {
    compile() {
      this.error = null

      axios.put('http://localhost:3000/compile', this.source)
        .catch((err) => {
          this.error = err
        })
        .then((res) => {
          console.log(res)

          if (!res) {
            return
          }

          if (res.data.error) {
            const content = document.getElementById('content')
            if (content) {
              content.innerHTML = ''
            }

            this.error = res.data.error
          } else {
            eval(res.data.content +
              '\nwindow.$route = $route\nwindow.$load("content", "/")')
          }
        })
    },

    insertTab() {
      let v = this.$refs.code.value
      let s = this.$refs.code.selectionStart
      let e = this.$refs.code.selectionEnd

      const indentSpacing = '  '

      this.$refs.code.value = v.substring(0, s)
        + indentSpacing + v.substring(e)

      this.$refs.code.selectionStart = s + indentSpacing.length
      this.$refs.code.selectionEnd = s + indentSpacing.length

      return false
    },

    checkCompile(e) {
      if (e) {
        this.compile()
      }
    },

    switchSource(source) {
      this.source = source

      this.compile()

      location.hash = '#source-input'
    }
  }
}
</script>

<style>
#content button {
  @apply border rounded px-2 py-1 block my-2;
}
</style>
