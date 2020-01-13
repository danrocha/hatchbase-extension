<template>
  <div class="p-4">
    <header class="flex items-center mb-8">
      <img src="images/logo.png" class="w-8 h-8 mr-2" />
      <h1 class="font-bold text-black">hatchbase</h1>
    </header>
    <div v-if="tab" class="mb-4">
      <div class="p-4 border border-black rounded-t">
        <p class="mb-1 text-lg font-bold leading-tight">{{ tab.title }}</p>
        <p class="text-gray-500">{{ tab.url }}</p>
      </div>
      <button @click="send(tab)" class="w-full p-2 -mt-1 text-center text-white bg-black border border-black rounded-b hover:shadow hover:bg-yellow-500 hover:text-black">
        Send to <strong>hatchbase</strong> &rarr;
      </button>
    </div>
    <p class="mb-8"><input type="checkbox" v-model="redirectToHatchbase" @change="toggleRedirectToHatchbase" /> Redirect to hatchbase after adding</p>
    <p class="text-xs text-center"><a href="https://hatchbase.io" target="_blank" class="text-blue-500 underline">go to hatchbase</a></p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tab: null,
      redirectToHatchbase: null,
    };
  },
  mounted() {
    chrome.tabs.query({ active: true }, tabs => {
      [this.tab] = tabs;
      return this.tab;
    });
    chrome.storage.sync.get(['redirectToHatchbase'], result => {
      console.log(`Value currently is ${result.redirectToHatchbase}`);
      this.redirectToHatchbase = result.redirectToHatchbase;
    });
  },
  methods: {
    send(tab) {
      const tabUrl = encodeURIComponent(tab.url);
      const tabTitle = encodeURIComponent(tab.title);
      const redirect = this.redirectToHatchbase ? 'hatchbase' : 'ad';
      const hatchbaseUrl = `http://localhost:3000/add?url=${tabUrl}&title=${tabTitle}&redirect=${redirect}`;

      // Update the url here.
      window.close();
      chrome.tabs.update(tab.id, { url: hatchbaseUrl });
    },
    toggleRedirectToHatchbase() {
      const { redirectToHatchbase } = this;

      // eslint-disable-next-line prettier/prettier
      chrome.storage.sync.set({'redirectToHatchbase': redirectToHatchbase}, () => {
        console.log(`Value is set to ${redirectToHatchbase}`);
      });
      chrome.storage.sync.get(['redirectToHatchbase'], result => {
        console.log(`Value currently is ${result.redirectToHatchbase}`);
      });
    },
  },
};
</script>

<style scoped></style>
