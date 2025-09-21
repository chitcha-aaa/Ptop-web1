<script setup lang="ts">
const userListData = useState('userListData')
</script>

<template>
  <div class="flex h-screen w-screen">
    <div class="m-auto w-1/2">

      <div class="border border-amber-500 rounded-2xl p-5 mb-5">
        {{ userListData }}
      </div>
      <Datacard />
    </div>
  </div>
</template>
