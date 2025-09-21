<script setup>
import { ref, computed, onMounted } from 'vue'

const registrations = ref([])
const q = ref('')
const status = ref('all') // all | active | pending | blocked
const page = ref(1)
const pageSize = ref(8)

const loadData = () => {
  if (typeof window === 'undefined') return
  const saved = localStorage.getItem('registrations')
  if (saved) {
    try {
      registrations.value = JSON.parse(saved)
    } catch {}
  }
  if (registrations.value.length === 0) {
    registrations.value = Array.from({ length: 15 }).map((_, i) => ({
      id: String(i + 1),
      name: `User ${i + 1}`,
      email: `user${i + 1}@example.com`,
      phone: `08000000${String(i + 1).padStart(2, '0')}`,
      status: i % 3 === 0 ? 'active' : i % 3 === 1 ? 'pending' : 'blocked',
      createdAt: new Date(Date.now() - i * 86400000).toISOString()
    }))
  }
}

onMounted(loadData)

const filtered = computed(() => {
  const term = q.value.trim().toLowerCase()
  let data = [...registrations.value]
  if (term) {
    data = data.filter(r =>
      r.name.toLowerCase().includes(term) ||
      r.email.toLowerCase().includes(term) ||
      r.phone.toLowerCase().includes(term)
    )
  }
  if (status.value !== 'all') data = data.filter(r => r.status === status.value)
  return data.sort((a, b) => +new Date(b.createdAt) - +new Date(a.createdAt))
})

const paged = computed(() => {
  const start = (page.value - 1) * pageSize.value
  return filtered.value.slice(start, start + pageSize.value)
})

const total = computed(() => filtered.value.length)
const fmtDate = (iso) => new Date(iso).toLocaleString()

const resetFilters = () => {
  q.value = ''
  status.value = 'all'
  page.value = 1
}

const goRegister = async () => { await navigateTo('/register') }
</script>

<template>
  <div class="min-h-screen bg-gray-50">
    <div class="max-w-6xl mx-auto p-4 sm:p-6">
      <!-- Header -->
      <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-3 mb-6">
        <div>
          <h1 class="text-2xl font-bold text-gray-900">รายการลงทะเบียน</h1>
          <p class="text-sm text-gray-600">แสดงข้อมูลผู้ลงทะเบียน ค้นหา กรอง และไปสมัครใหม่</p>
        </div>
        <div class="flex gap-2">
          <button @click="goRegister" class="px-4 py-2 rounded-lg bg-blue-600 text-white hover:bg-blue-700">ไปหน้าสมัคร</button>
        </div>
      </div>

      <!-- Filters -->
      <div class="bg-white border border-gray-200 rounded-xl p-4 mb-4">
        <div class="grid grid-cols-1 md:grid-cols-4 gap-3">
          <div class="md:col-span-2">
            <input v-model="q" type="text" placeholder="ค้นหา ชื่อ/อีเมล/เบอร์โทร" class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent" />
          </div>
          <div>
            <select v-model="status" class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
              <option value="all">สถานะทั้งหมด</option>
              <option value="active">Active</option>
              <option value="pending">Pending</option>
              <option value="blocked">Blocked</option>
            </select>
          </div>
          <div class="flex gap-2">
            <button @click="resetFilters" class="px-4 py-2 rounded-lg border border-gray-300 text-gray-700 hover:bg-gray-100">ล้างตัวกรอง</button>
            <button @click="page = 1" class="px-4 py-2 rounded-lg bg-gray-900 text-white hover:bg-black">ใช้ตัวกรอง</button>
          </div>
        </div>
      </div>

      <!-- Table -->
      <div class="bg-white border border-gray-200 rounded-xl overflow-hidden">
        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">ชื่อ</th>
                <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">อีเมล</th>
                <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">เบอร์โทร</th>
                <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">สถานะ</th>
                <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">วันที่สร้าง</th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr v-for="r in paged" :key="r.id" class="hover:bg-gray-50">
                <td class="px-4 py-3 text-sm text-gray-900 font-medium">{{ r.name }}</td>
                <td class="px-4 py-3 text-sm text-gray-700">{{ r.email }}</td>
                <td class="px-4 py-3 text-sm text-gray-700">{{ r.phone }}</td>
                <td class="px-4 py-3 text-sm">
                  <span :class="[
                    'px-2 py-1 rounded-full text-xs font-medium',
                    r.status === 'active' ? 'bg-green-100 text-green-700' : r.status === 'pending' ? 'bg-yellow-100 text-yellow-700' : 'bg-red-100 text-red-700'
                  ]">{{ r.status }}</span>
                </td>
                <td class="px-4 py-3 text-sm text-gray-500">{{ fmtDate(r.createdAt) }}</td>
              </tr>
              <tr v-if="paged.length === 0">
                <td colspan="5" class="px-4 py-6 text-center text-gray-500">ไม่พบข้อมูล</td>
              </tr>
            </tbody>
          </table>
        </div>
        <!-- Pagination -->
        <div class="flex items-center justify-between px-4 py-3 border-t border-gray-200">
          <div class="text-sm text-gray-600">ทั้งหมด {{ total }} รายการ</div>
          <div class="flex items-center gap-2">
            <button :disabled="page === 1" @click="page = Math.max(1, page - 1)" class="px-3 py-2 rounded-lg border border-gray-300 text-gray-700 disabled:opacity-50">ก่อนหน้า</button>
            <span class="text-sm text-gray-600">หน้า {{ page }} / {{ Math.max(1, Math.ceil(total / pageSize)) }}</span>
            <button :disabled="page >= Math.ceil(total / pageSize)" @click="page = Math.min(Math.ceil(total / pageSize), page + 1)" class="px-3 py-2 rounded-lg border border-gray-300 text-gray-700 disabled:opacity-50">ถัดไป</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
