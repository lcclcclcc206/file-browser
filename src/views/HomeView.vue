<script setup lang="ts">
import axios from 'axios'
import { inject, ref } from 'vue';

const directorylist = ref([] as string[]);

let reqpath = '/api/browser';

axios.get(reqpath)
  .then(res => {
    const list = res.data as string[];
    list.forEach(value => {
      directorylist.value.push(value);
    });
  });

function getDirectoryUrl(dirname: string) {
  return `/filebrowser/${dirname}`;
}
</script>

<template>
  <main>
    <div v-for="folder in directorylist">
      <a :href="getDirectoryUrl(folder)">{{ folder }}</a>
    </div>
  </main>
</template>
