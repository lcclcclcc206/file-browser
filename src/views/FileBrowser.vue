<script setup lang="ts">
import axios from 'axios';
import { ref } from 'vue';
import { useRoute } from 'vue-router';

const directorylist = ref([] as string[]);
const filelist = ref([] as string[]);
const browser_relativepath = ref('');

const route = useRoute();
let dir = route.params.dirname as string;
if (dir == '')
    dir = 'FileBrowser';

let reqpath = `/api/browser/${dir}`;

axios.get(reqpath)
    .then(res => {
        let list = res.data.dirList as string[];
        list.forEach(value => {
            directorylist.value.push(value);
        });
        list = res.data.fileList as string[];
        list.forEach(value => {
            filelist.value.push(value);
        });
    });

function getFileUrl(file: string) {
    return reqpath.concat(`/file/${file}`, `?relativepath=${browser_relativepath.value}`);
}

function updateView(relativepath: string) {
    directorylist.value = [];
    filelist.value = [];
    const path = reqpath.concat(`?relativepath=${relativepath}`)
    axios.get(path)
        .then(res => {
            let list = res.data.dirList as string[];
            list.forEach(value => {
                directorylist.value.push(value);
            });
            list = res.data.fileList as string[];
            list.forEach(value => {
                filelist.value.push(value);
            });
        });
}

function enterDirectory(dirname: string) {
    browser_relativepath.value = browser_relativepath.value.concat(`/${dirname}`)
    updateView(browser_relativepath.value);
}

function goBack() {
    let index = browser_relativepath.value.lastIndexOf('/');
    if (index === -1) {
        browser_relativepath.value = '';
    }
    else {
        browser_relativepath.value = browser_relativepath.value.substring(0, index);
    }
    updateView(browser_relativepath.value);
}
</script>

<template>
    <main>
        <button v-if="browser_relativepath != ''" @click="goBack">return</button>
        <p>{{ browser_relativepath }}</p>
        <h2>Directory:</h2>
        <div v-for="folder in directorylist">
            <button @click="enterDirectory(folder)">{{ folder }}</button>
        </div><br/>
        <h2>File:</h2>
        <div v-for="file in filelist">
            <a :href="getFileUrl(file)" target="_blank">{{ file }}</a>
        </div>
    </main>
</template>