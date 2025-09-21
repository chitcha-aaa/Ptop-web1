<script setup lang="ts">
const userListData = useState<unknown[]>('userListData', () => [])
async function loadData() {
 let res = await fetch('http://localhost:4000/user-list')
 userListData.value = await res.json()
}
onMounted(async () => {
  await loadData()
})
</script>

<template>
  <div class="border rounded-2xl p-3">
    <div class="flex flex-col gap-3">
        <h2 class="text-xl">user list</h2>
      <UTable :data="userListData" class="flex-1" />
    </div>
  </div>
</template>
