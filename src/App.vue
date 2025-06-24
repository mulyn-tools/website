<template>
  <div class="main">
    <el-container>
      <el-header>炑铃老师的歌单</el-header>
      <el-main>
        <div class="mt-4">
          <el-input v-model="search" class="input-with-select">
            <template #prepend>
              <el-select v-model="select" placeholder="Select" style="width: 115px">
                <el-option label="歌曲名" value="0" />
                <el-option label="歌手" value="1" />
              </el-select>
            </template>
          </el-input>
        </div>
        <el-table v-on:row-click="(event: Song) => copyToClipboard(event)" :data="tableData" style="width: 100%">
          <el-table-column prop="name" label="歌名" width="180" />
          <el-table-column prop="author" label="歌手" width="180" />
          <el-table-column prop="note" label="备注" />
        </el-table>
      </el-main>
    </el-container>
  </div>
</template>

<script lang="ts" setup>
import { ref, watch } from 'vue'
import { ElMessage } from 'element-plus'
const tableData = ref([])
const search = ref('')
const select = ref("0")

interface Song {
  name: string,
  author: string,
  note?: string
}

fetch(`${import.meta.env.VITE_BACKEND_URL}/playlist`).then(resp => resp.json()).then(json => {
  tableData.value = json
})

const copyToClipboard = async (event: Song) => {
  await navigator.clipboard.writeText(`点歌 ${event.name}`)
  ElMessage({
    message: '复制成功',
    grouping: true,
    type: 'success',
  })
}

watch(search, (new_question) => {
  if (select.value == "0") {
    fetch(`${import.meta.env.VITE_BACKEND_URL}/playlist?song_name=${new_question}`).then(resp => resp.json()).then(json => {
      tableData.value = json
    })
  } else if (select.value == "1") {
    fetch(`${import.meta.env.VITE_BACKEND_URL}/playlist?author=${new_question}`).then(resp => resp.json()).then(json => {
      tableData.value = json
    })
  }
})

</script>

<style>
.input-with-select .el-input-group__prepend {
  background-color: var(--el-fill-color-blank);
}

body {
  background-image: background;
}
</style>
