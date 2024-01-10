<script setup lang="ts">
import Copy from '~/components/icons/copy.vue'

// TODO: add input
// const inputUrl = ref<String>('')
const url = ref<String>('')
// 解析完成的URL
const urlObj = ref<any>({})

const copyTokeyboard = (text: string) => {
  navigator.clipboard.writeText(text)
}

const parseURL = (urlStr: string) => {
  const url = new URL(urlStr)

  return {
    protocol: url.protocol,
    host: url.host,
    pathname: url.pathname,
    search: url.search,
    hash: url.hash,
    href: url.href,
    searchParams: url.searchParams,
  }
}

// get uer tab url
const getUrl = () => {
  return browser.tabs.query({ active: true, currentWindow: true })
    .then((tabs) => {
      url.value = tabs[0].url as string
      urlObj.value = parseURL(url.value as string)
    })
    .catch(error => console.error(error))
}

getUrl()

const parseSearch = (search: string): Array<any> => {
  const searchParams = new URLSearchParams(search.split('?')[1] || '')
  return Array.from(searchParams.entries())
}

const parseHash = (hash: string) => {
  const hashPath = hash.split('?')[0]
  const hashParams = new URLSearchParams(hash.split('?')[1] || '')
  return {
    hashSearch: Array.from(hashParams.entries()),
    hash: hashPath,
  }
}

const updateUrl = () => {
  console.log('url')
}

const goToGithub = () => {
  browser.tabs.create({ url: 'https://github.com/lx2009/url-editor' })
}
</script>

<template>
  <main class="w-[500px] text-gray-700">
    <div flex justify-between pt-2 px-2 bg-gray-1 hover:cursor-pointer>
      <h1 text="lg" font-600>
        URL Editor
      </h1>
      <div>
        <mdi:github class="h-[20px] w-[20px]" @click="goToGithub" />
      </div>
    </div>

    <!-- TODO: input url -->
    <!-- <div class="px-2 border-b pb-2">
      <input :value="inputUrl" class="input py-1" placeholder="可以输入URL解析">
    </div> -->

    <div text="14px">
      <div border-b py-1 px-2 hover="cursor-pointer bg-[#FAFAFA]">
        <strong>Protocol: </strong>
        <div class="flex items-center">
          <Copy mt-1 mr-1 hover:text-gray-500 @click="copyTokeyboard(urlObj.protocol)" />
          <textarea :value="urlObj.protocol" class="text-area" rows="1" />
        </div>
      </div>
      <div border-b py-1 px-2 hover="cursor-pointer bg-[#FAFAFA]">
        <strong>Host: </strong>
        <div class="flex items-center">
          <Copy mt-1 mr-1 hover:text-gray-500 @click="copyTokeyboard(urlObj.host)" />
          <textarea :value=" urlObj.host" class="text-area" rows="1" />
        </div>
      </div>
      <div border-b py-1 px-2 hover="cursor-pointer bg-[#FAFAFA]">
        <strong>Path: </strong>
        <div class="flex items-center">
          <Copy mt-1 mr-1 hover:text-gray-500 @click="copyTokeyboard(urlObj.pathname)" />
          <textarea :value=" urlObj.pathname" class="text-area" rows="1" />
        </div>
      </div>
      <div v-if="urlObj.search" border-b py-1 px-2>
        <strong>Search: </strong>
        <div v-for="item in parseSearch(urlObj.search)" :key="item[0]" class="flex items-center">
          <Copy mt-1 hover:text-gray-500 @click="copyTokeyboard(item[1])" />
          <div mx-2 text mt-1 class="w-[50px]">
            {{ item[0] }}
          </div>
          <textarea :value="item[1]" class="text-area" rows="1" />
        </div>
      </div>

      <div v-if="urlObj.hash" border-b py-1 px-2>
        <strong>Hash: </strong>
        <div class="flex items-center">
          <Copy mt-1 hover:text-gray-500 @click="copyTokeyboard(parseHash(urlObj.hash).hash)" />
          <div mx-2 text mt-1 class="w-[50px]">
            hash
          </div>
          <textarea :value="parseHash(urlObj.hash).hash" class="text-area" rows="1" />
        </div>
        <div v-for="item in parseHash(urlObj.hash).hashSearch" :key="item[0]" class="flex items-center">
          <Copy mt-1 hover:text-gray-500 @click="copyTokeyboard(item[1])" />
          <div mx-2 text mt-1 class="flex-1 min-w-[50px]">
            {{ item[0] }}
          </div>
          <textarea :value="item[1]" class="text-area" rows="1" />
        </div>
      </div>
    </div>

    <!-- bottom -->
    <div p-2>
      <button class="btn" @click="updateUrl">
        Update
      </button>
    </div>
  </main>
</template>
