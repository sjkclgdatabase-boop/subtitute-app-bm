<template>
  <div class="p-8 max-w-7xl mx-auto min-h-screen">
    
    <!-- 头部区域 -->
    <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-8 gap-4">
      <div>
        <h1 class="text-4xl font-extrabold tracking-tight text-transparent bg-clip-text bg-gradient-to-r from-slate-900 via-indigo-800 to-violet-800">
          PUSAT PENGURUSAN GURU GANTI
        </h1>
        <p class="text-slate-500 text-sm mt-2 font-medium">PEMANTAUAN CUTI GURU SECARA MASA NYATA, MENJEJAK PROSES AGIHAN TUGASAN GURU GANTI</p>
      </div>
      <router-link to="/leave-entry" class="group relative inline-flex items-center justify-center px-6 py-2.5 text-sm font-semibold text-white transition-all duration-200 bg-slate-900 font-display rounded-full hover:bg-slate-800 hover:shadow-lg hover:shadow-slate-900/20 hover:-translate-y-0.5">
        <span>DAFTAR CUTI BAHARU</span>
        <svg class="w-4 h-4 ml-2 group-hover:translate-x-1 transition-transform" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"/></svg>
      </router-link>
    </div>

    <!-- 核心指标统计卡片 -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
      <div class="bg-white rounded-2xl p-6 shadow-sm ring-1 ring-slate-900/5 hover:shadow-md transition-all">
        <div class="flex items-center justify-between">
          <p class="text-slate-500 text-sm font-semibold uppercase tracking-wider">TUGASAN GURU GANTI BELUM DIAGIHKAN</p>
          <div class="w-10 h-10 rounded-full bg-amber-50 text-amber-600 flex items-center justify-center ring-1 ring-amber-500/20">⏳</div>
        </div>
        <p class="text-4xl font-bold text-slate-900 mt-4">{{ pendingCount }}</p>
      </div>
      
      <div class="bg-white rounded-2xl p-6 shadow-sm ring-1 ring-slate-900/5 hover:shadow-md transition-all">
        <div class="flex items-center justify-between">
          <p class="text-slate-500 text-sm font-semibold uppercase tracking-wider">Pengagihan Selesai</p>
          <div class="w-10 h-10 rounded-full bg-emerald-50 text-emerald-600 flex items-center justify-center ring-1 ring-emerald-500/20">✓</div>
        </div>
        <p class="text-4xl font-bold text-slate-900 mt-4">{{ assignedCount }}</p>
      </div>
      
      <div class="bg-white rounded-2xl p-6 shadow-sm ring-1 ring-slate-900/5 hover:shadow-md transition-all">
        <div class="flex items-center justify-between">
          <p class="text-slate-500 text-sm font-semibold uppercase tracking-wider">JUMLAH KESELURUHAN TUGASAN</p>
          <div class="w-10 h-10 rounded-full bg-indigo-50 text-indigo-600 flex items-center justify-center ring-1 ring-indigo-500/20">📊</div>
        </div>
        <p class="text-4xl font-bold text-slate-900 mt-4">{{ leaveRequests.length }}</p>
      </div>
    </div>

    <!-- 过滤器与视图切换栏 -->
    <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center mb-6 gap-4 bg-white p-4 rounded-2xl shadow-sm ring-1 ring-slate-900/5">
      <div class="flex flex-wrap items-center gap-3">
        <div class="flex bg-slate-100 p-1 rounded-xl">
          <button 
            @click="viewMode = 'today'"
            :class="viewMode === 'today' ? 'bg-white text-slate-900 shadow-sm' : 'text-slate-500 hover:text-slate-900'"
            class="px-4 py-2 rounded-lg text-xs font-bold transition-all cursor-pointer"
          >
            📅 HARI INI SAHAJA
          </button>
          <button 
            @click="viewMode = 'grouped'"
            :class="viewMode === 'grouped' ? 'bg-white text-slate-900 shadow-sm' : 'text-slate-500 hover:text-slate-900'"
            class="px-4 py-2 rounded-lg text-xs font-bold transition-all cursor-pointer"
          >
            📂 MENGIKUT TARIKH
          </button>
        </div>

        <input 
          v-if="viewMode === 'today'"
          type="date" 
          v-model="targetDate"
          class="bg-slate-50 border border-slate-200 px-3 py-1.5 rounded-xl text-xs font-semibold text-slate-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 cursor-pointer"
        />
      </div>

      <div class="text-xs text-slate-400 font-medium">
        PAPARAN SEMASA <span class="font-bold text-slate-700">{{ displayedRequests.length }}</span> REKOD CUTI
      </div>
    </div>

    <!-- 数据表格区域 -->
    <div class="bg-white rounded-2xl shadow-sm ring-1 ring-slate-900/5 overflow-hidden">
      
      <!-- 模式一：只看今天 -->
      <div v-if="viewMode === 'today'" class="overflow-x-auto">
        <table class="w-full text-left border-collapse whitespace-nowrap">
          <thead>
            <tr class="bg-slate-50/50 border-b border-slate-100 text-slate-500 text-xs uppercase tracking-widest font-semibold select-none">
              <th class="py-4 pl-6 pr-4 cursor-pointer hover:text-indigo-600 transition" @click="handleSort('teacher')">
                GURU CUTI <span v-if="sortKey === 'teacher'" class="text-indigo-600 font-bold ml-1">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
              </th>
              <th class="p-4 cursor-pointer hover:text-indigo-600 transition" @click="handleSort('class')">
                KELAS / SUBJEK <span v-if="sortKey === 'class'" class="text-indigo-600 font-bold ml-1">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
              </th>
              <th class="p-4 cursor-pointer hover:text-indigo-600 transition" @click="handleSort('period')">
                SLOT MASA <span v-if="sortKey === 'period'" class="text-indigo-600 font-bold ml-1">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
              </th>
              <th class="p-4 cursor-pointer hover:text-indigo-600 transition" @click="handleSort('status')">
                STATUS TUGASAN <span v-if="sortKey === 'status'" class="text-indigo-600 font-bold ml-1">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
              </th>
              <th class="p-4 text-left cursor-pointer hover:text-indigo-600 transition" @click="handleSort('substitute')">
                GURU GANTI <span v-if="sortKey === 'substitute'" class="text-indigo-600 font-bold ml-1">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
              </th>
              <th class="py-4 pr-6 pl-4 text-right">TINDAKAN</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-100 text-sm">
            <tr v-if="displayedRequests.length === 0">
              <td colspan="6" class="py-12 text-center text-slate-400 font-medium">TIADA REKOD CUTI GURU PADA TARIKH ({{ targetDate }}) </td>
            </tr>
            <tr v-for="req in displayedRequests" :key="req.id" class="hover:bg-slate-50/50 transition-colors group">
              <td class="py-4 pl-6 pr-4 font-bold text-slate-900">
                {{ teachersMap[req.teacher_id]?.name || 'SEDANG DIMUAT...' }}
              </td>
              <td class="p-4">
                <div class="flex items-center gap-2">
                  <span class="text-slate-900 font-medium">{{ req.class_name }}</span>
                  <span v-if="req.class_name.includes('/')" class="px-1.5 py-0.5 bg-violet-100 text-violet-700 rounded text-[10px] font-bold">KELAS GABUNGAN</span>
                  <span class="text-slate-400">·</span>
                  <span class="text-slate-500">{{ req.subject }}</span>
                </div>
              </td>
              <td class="p-4">
                <span class="inline-flex items-center px-2.5 py-0.5 rounded-md bg-slate-100 text-slate-600 font-medium text-xs">
                  SESI KE-{{ req.period }}
                </span>
              </td>
              <td class="p-4">
                <span :class="statusClass(req.status)" class="inline-flex items-center px-2.5 py-1 rounded-full text-xs font-semibold ring-1 ring-inset">
                  {{ req.status === 'pending' ? 'BELUM DITETAPKAN' : 'TELAH DITETAPKAN' }}
                </span>
              </td>
              <td class="p-4 text-left">
                <span v-if="req.status === 'assigned'" class="text-xs font-bold text-indigo-700 bg-indigo-50 px-3 py-1 rounded-full border border-indigo-100 inline-block">
                  {{ getSubstituteDisplay(req.id) }}
                </span>
                <span v-else class="text-xs text-slate-400 font-medium">-</span>
              </td>
              <td class="py-4 pr-6 pl-4 text-right">
                <button 
                  v-if="req.status === 'pending'"
                  @click="openRecommendModal(req)"
                  class="inline-flex items-center justify-center px-4 py-1.5 text-xs font-semibold text-indigo-700 bg-indigo-50 rounded-full hover:bg-indigo-100 transition-all cursor-pointer"
                >
                  ✨Penjadualan Pintar & Manual
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- 模式二：按日期折叠归类 -->
      <div v-else class="p-6 space-y-4">
        <div v-if="Object.keys(groupedRequests).length === 0" class="py-12 text-center text-slate-400 font-medium">
          TIADA SEBARANG REKOD CUTI
        </div>

        <div v-for="(requests, date) in groupedRequests" :key="date" class="border border-slate-200 rounded-2xl overflow-hidden shadow-sm">
          <button 
            @click="toggleDateFold(date)"
            class="w-full flex justify-between items-center px-6 py-4 bg-slate-50 hover:bg-slate-100 transition text-left cursor-pointer"
          >
            <div class="flex items-center gap-3">
              <span class="font-bold text-slate-900 text-base">📅 {{ date }}</span>
              <span class="text-xs px-2.5 py-0.5 rounded-full bg-indigo-100 text-indigo-700 font-semibold">
                {{ requests.length }} SLOT TUGASAN
              </span>
            </div>
            <span class="text-slate-400 text-sm font-semibold">
              {{ foldedDates[date] ? 'BUKA ▼' : 'TUTUP ▲' }}
            </span>
          </button>

          <div v-show="!foldedDates[date]" class="overflow-x-auto bg-white">
            <table class="w-full text-left border-collapse whitespace-nowrap">
              <thead>
                <tr class="bg-slate-50/50 border-b border-slate-100 text-slate-500 text-xs uppercase tracking-widest font-semibold select-none">
                  <th class="py-3 pl-6 pr-4 w-48 cursor-pointer hover:text-indigo-600 transition" @click="handleSort('teacher')">
                    GURU CUTI <span v-if="sortKey === 'teacher'" class="text-indigo-600 font-bold ml-1">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
                  </th>
                  <th class="p-3 cursor-pointer hover:text-indigo-600 transition" @click="handleSort('class')">
                    KELAS / SUBJEK <span v-if="sortKey === 'class'" class="text-indigo-600 font-bold ml-1">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
                  </th>
                  <th class="p-3 cursor-pointer hover:text-indigo-600 transition" @click="handleSort('period')">
                    SLOT MASA <span v-if="sortKey === 'period'" class="text-indigo-600 font-bold ml-1">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
                  </th>
                  <th class="p-3 cursor-pointer hover:text-indigo-600 transition" @click="handleSort('status')">
                    STATUS <span v-if="sortKey === 'status'" class="text-indigo-600 font-bold ml-1">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
                  </th>
                  <th class="p-3 text-left cursor-pointer hover:text-indigo-600 transition" @click="handleSort('substitute')">
                    TUGASAN GURU GANTI <span v-if="sortKey === 'substitute'" class="text-indigo-600 font-bold ml-1">{{ sortOrder === 'asc' ? '▲' : '▼' }}</span>
                  </th>
                  <th class="py-3 pr-6 pl-3 text-right">TINDAKAN</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-slate-100 text-sm">
                <tr v-for="req in requests" :key="req.id" class="hover:bg-slate-50/50 transition-colors group">
                  <td class="py-3 pl-6 pr-4 text-slate-900 font-bold w-48">
                    {{ teachersMap[req.teacher_id]?.name || 'SEDANG DIMUAT...' }}
                  </td>
                  <td class="p-3">
                    <div class="flex items-center gap-2">
                      <span class="text-slate-900 font-medium">{{ req.class_name }}</span>
                      <span v-if="req.class_name.includes('/')" class="px-1.5 py-0.5 bg-violet-100 text-violet-700 rounded text-[10px] font-bold">KELAS GABUNGAN</span>
                      <span class="text-slate-400">·</span>
                      <span class="text-slate-500">{{ req.subject }}</span>
                    </div>
                  </td>
                  <td class="p-3">
                    <span class="inline-flex items-center px-2.5 py-0.5 rounded-md bg-slate-100 text-slate-600 font-medium text-xs">
                      SESI KE-{{ req.period }}
                    </span>
                  </td>
                  <td class="p-3">
                    <span :class="statusClass(req.status)" class="inline-flex items-center px-2.5 py-1 rounded-full text-xs font-semibold ring-1 ring-inset">
                      {{ req.status === 'pending' ? 'BELUM DITETAPKAN' : 'TELAH DITETAPKAN' }}
                    </span>
                  </td>
                  <td class="p-3 text-left">
                    <span v-if="req.status === 'assigned'" class="text-xs font-bold text-indigo-700 bg-indigo-50 px-3 py-1 rounded-full border border-indigo-100 inline-block">
                      {{ getSubstituteDisplay(req.id) }}
                    </span>
                    <span v-else class="text-xs text-slate-400 font-medium">-</span>
                  </td>
                  <td class="py-3 pr-6 pl-3 text-right">
                    <button 
                      v-if="req.status === 'pending'"
                      @click="openRecommendModal(req)"
                      class="inline-flex items-center justify-center px-4 py-1.5 text-xs font-semibold text-indigo-700 bg-indigo-50 rounded-full hover:bg-indigo-100 transition-all cursor-pointer"
                    >
                      ✨ JADUAL
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

    </div>

    <!-- 弹窗 -->
    <transition enter-active-class="transition duration-300 ease-out" enter-from-class="opacity-0 scale-95" enter-to-class="opacity-100 scale-100" leave-active-class="transition duration-200 ease-in" leave-from-class="opacity-100 scale-100" leave-to-class="opacity-0 scale-95">
      <div v-if="showModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 sm:p-6">
        <div class="absolute inset-0 bg-slate-900/30 backdrop-blur-sm" @click="showModal = false"></div>
        <div class="relative bg-white rounded-3xl shadow-2xl w-full max-w-2xl overflow-hidden ring-1 ring-slate-900/10 max-h-[90vh] flex flex-col">
          
          <div class="px-8 py-6 border-b border-slate-100 flex justify-between items-center bg-white/50 backdrop-blur-md shrink-0">
            <div>
              <h2 class="text-xl font-bold text-slate-900">PUSAT PENETAPAN GURU GANTI</h2>
              <p class="text-sm text-slate-500 mt-1">SOKONGAN CADANGAN PINTAR, ATAU PILIH MANA-MANA GURU SESI YANG SAMA SECARA MANUAL DI BAWAH</p>
            </div>
            <button @click="showModal = false" class="text-slate-400 hover:text-slate-600 bg-slate-100 hover:bg-slate-200 rounded-full p-2 transition cursor-pointer">×</button>
          </div>
          
          <div class="p-8 bg-slate-50/50 space-y-6 overflow-y-auto">
            
            <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm flex items-center gap-3">
              <span class="text-xs font-bold text-slate-700 whitespace-nowrap">📍 LOKASI / CATATAN:</span>
              <input 
                v-model="assignmentRemark" 
                type="text" 
                placeholder="CONTOH: PERPUSTAKAAN (JIKA PERLU BAWA KE PERPUSTAKAAN ATAU GABUNG KELAS)" 
                class="w-full px-3 py-1.5 bg-slate-50 border border-slate-200 rounded-xl text-xs font-medium text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500/50"
              />
            </div>

            <div class="bg-indigo-50/60 p-5 rounded-2xl border border-indigo-100 shadow-sm">
              <h3 class="text-xs font-bold uppercase tracking-wider text-indigo-900 mb-3 flex items-center gap-2">
                <span>🛠️ TETAPAN MANUAL (TANPA CADANGAN PINTAR)</span>
              </h3>
              <div class="flex flex-col sm:flex-row items-center gap-3">
                <select 
                  v-model="manualSelectedTeacherId" 
                  class="w-full px-3 py-2 bg-white border border-indigo-200 rounded-xl text-xs font-semibold text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500 cursor-pointer"
                >
                  <option value="" disabled>-- SILA PILIH GURU SESI SAMA SECARA MANUAL --</option>
                  <option v-for="t in allSameSessionTeachers" :key="t.id" :value="t.id">
                    {{ t.name }} (SUBJEK: {{ t.subject || 'TIADA' }})
                  </option>
                </select>
                <button 
                  @click="assignSubstitute(manualSelectedTeacherId)" 
                  :disabled="!manualSelectedTeacherId"
                  class="w-full sm:w-auto bg-indigo-600 hover:bg-indigo-700 disabled:opacity-50 text-white px-5 py-2 rounded-xl text-xs font-semibold shadow-sm transition-all shrink-0 cursor-pointer"
                >
                  SAHKAN TETAPAN MANUAL
                </button>
              </div>
            </div>

            <hr class="border-slate-200" />

            <div>
              <h3 class="text-xs font-bold uppercase tracking-wider text-slate-500 mb-3">✨ SENARAI CALON CADANGAN PINTAR (TOP 6)</h3>
              
              <div v-if="loading" class="flex flex-col items-center justify-center py-6 space-y-3">
                <div class="w-6 h-6 border-4 border-indigo-500/30 border-t-indigo-600 rounded-full animate-spin"></div>
                <p class="text-xs text-slate-500 font-medium">ALGORITMA PINTAR SEDANG DIKIRA...</p>
              </div>
              
              <div v-else-if="recommendations.length === 0" class="bg-white p-4 rounded-2xl border border-slate-200 text-xs text-slate-500 text-center">
                TIADA CADANGAN AUTOMATIK, SILA GUNAKAN TETAPAN MANUAL DI ATAS.
              </div>

              <div v-else class="space-y-3">
                <div v-for="(teacher, index) in recommendations" :key="teacher.id" class="group flex flex-col sm:flex-row sm:justify-between sm:items-center p-4 bg-white border border-slate-200 rounded-2xl hover:border-indigo-300 hover:shadow-sm transition-all">
                  <div class="flex items-center gap-3 mb-3 sm:mb-0">
                    <div class="w-8 h-8 rounded-full bg-gradient-to-br from-indigo-100 to-violet-100 text-indigo-700 font-extrabold flex items-center justify-center text-xs">
                      #{{ index + 1 }}
                    </div>
                    <div>
                      <div class="font-bold text-slate-900 text-sm flex items-center gap-2">
                        {{ teacher.name }} 
                        <span class="text-[10px] font-medium px-2 py-0.5 rounded-md bg-slate-100 text-slate-600">{{ teacher.subject }}</span>
                      </div>
                      <div class="text-[11px] text-slate-500 mt-1 flex items-center gap-2 flex-wrap">
                        <span>JADUAL ASAL: <span class="font-bold text-slate-700">{{ teacher.originalClasses }} KELAS</span></span>
                        <span>·</span>
                        <span>JUMLAH GANTIAN HARI INI: <span class="font-bold text-orange-600">{{ teacher.todaySubCount }} KELAS</span></span>
                        <span>·</span>
                        <span>JUMLAH GANTIAN MINGGU INI: <span class="font-bold text-slate-700">{{ teacher.currentSubCount }}/{{ teacher.max_substitute_per_week }}</span></span>
                      </div>    
                    </div>
                  </div>
                  <button @click="assignSubstitute(teacher.id)" class="bg-slate-900 hover:bg-indigo-600 text-white px-4 py-2 rounded-xl text-xs font-semibold shadow-sm transition-all cursor-pointer">
                    TETAPAN PINTAR
                  </button>
                </div>
              </div>
            </div>

          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { supabase } from '../services/supabase'
import { recommendSubstitute } from '../utils/algorithm'
import { useToast } from '../utils/toast'

const toast = useToast()
const leaveRequests = ref([])
const substituteAssignments = ref({})
const teachersMap = ref({})

const showModal = ref(false)
const loading = ref(false)
const recommendations = ref([])
const currentRequest = ref(null)
const assignmentRemark = ref('')

const viewMode = ref('today')
const targetDate = ref(new Date().toISOString().split('T')[0])
const foldedDates = ref({})

const sortKey = ref('period')
const sortOrder = ref('asc')

const handleSort = (key) => {
  if (sortKey.value === key) {
    sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc'
  } else {
    sortKey.value = key
    sortOrder.value = 'asc'
  }
}

const sortFunction = (a, b) => {
  let valA, valB

  if (sortKey.value === 'teacher') {
    valA = teachersMap.value[a.teacher_id]?.name || ''
    valB = teachersMap.value[b.teacher_id]?.name || ''
  } else if (sortKey.value === 'class') {
    valA = `${a.class_name} ${a.subject}`
    valB = `${b.class_name} ${b.subject}`
  } else if (sortKey.value === 'period') {
    valA = Number(a.period)
    valB = Number(b.period)
  } else if (sortKey.value === 'status') {
    valA = a.status
    valB = b.status
  } else if (sortKey.value === 'substitute') {
    valA = getSubstituteDisplay(a.id)
    valB = getSubstituteDisplay(b.id)
  }

  if (valA < valB) return sortOrder.value === 'asc' ? -1 : 1
  if (valA > valB) return sortOrder.value === 'asc' ? 1 : -1
  return 0
}

const allSameSessionTeachers = ref([])
const manualSelectedTeacherId = ref('')

const pendingCount = computed(() => leaveRequests.value.filter(r => r.status === 'pending').length)
const assignedCount = computed(() => leaveRequests.value.filter(r => r.status === 'assigned').length)

const displayedRequests = computed(() => {
  const filtered = leaveRequests.value.filter(r => r.leave_date === targetDate.value)
  return filtered.sort(sortFunction)
})

const groupedRequests = computed(() => {
  const groups = {}
  leaveRequests.value.forEach(req => {
    if (!groups[req.leave_date]) {
      groups[req.leave_date] = []
    }
    groups[req.leave_date].push(req)
  })

  Object.keys(groups).forEach(date => {
    groups[date].sort(sortFunction)
  })

  return groups
})

const toggleDateFold = (date) => {
  foldedDates.value[date] = !foldedDates.value[date]
}

const fetchRequests = async () => {
  const { data: tData } = await supabase.from('teachers').select('id, name')
  if (tData) {
    tData.forEach(t => {
      teachersMap.value[t.id] = t
    })
  }

  const { data: lData, error } = await supabase
    .from('leave_requests')
    .select('*')
    .order('leave_date', { ascending: false })
  
  if (!error) {
    leaveRequests.value = lData || []

    const leaveIds = leaveRequests.value.map(l => l.id)
    if (leaveIds.length > 0) {
      const { data: sData } = await supabase
        .from('substitute_assignments')
        .select('*')
        .in('leave_request_id', leaveIds)
      
      if (sData) {
        const map = {}
        sData.forEach(sub => {
          map[sub.leave_request_id] = sub
        })
        substituteAssignments.value = map
      }
    }
  }
}

const getSubstituteDisplay = (leaveRequestId) => {
  const sub = substituteAssignments.value[leaveRequestId]
  if (!sub || !sub.sub_teacher_id) return ''
  
  const subTeacher = teachersMap.value[sub.sub_teacher_id]
  const name = subTeacher ? subTeacher.name : 'TIDAK DIKENALI'

  if (sub.remark) {
    return `${name} (${sub.remark})`
  }
  return name
}

const statusClass = (status) => {
  if (status === 'pending') return 'bg-amber-50 text-amber-700 ring-amber-600/20'
  return 'bg-emerald-50 text-emerald-700 ring-emerald-600/20'
}

const openRecommendModal = async (req) => {
  currentRequest.value = req
  assignmentRemark.value = ''
  manualSelectedTeacherId.value = ''
  showModal.value = true
  loading.value = true

  try {
    recommendations.value = await recommendSubstitute(req)

    const { data: absentTeacher } = await supabase
      .from('teachers')
      .select('session')
      .eq('id', req.teacher_id)
      .single()
    
    const session = absentTeacher?.session || 'morning'

    const { data: teachersData } = await supabase
      .from('teachers')
      .select('*')
      .eq('is_active', true)
      .eq('session', session)
      .neq('id', req.teacher_id)

    allSameSessionTeachers.value = teachersData || []
  } catch (error) {
    toast.error("GAGAL MEMUATKAN DATA JADUAL: " + error.message)
  } finally {
    loading.value = false
  }
}

const assignSubstitute = async (teacherId) => {
  if (!teacherId) return
  try {
    const { error: insertErr } = await supabase.from('substitute_assignments').insert({
      leave_request_id: currentRequest.value.id,
      sub_teacher_id: teacherId,
      remark: assignmentRemark.value ? assignmentRemark.value.trim().toUpperCase() : null
    })
    if (insertErr) throw insertErr

    const { error: updateErr } = await supabase.from('leave_requests')
      .update({ status: 'assigned' })
      .eq('id', currentRequest.value.id)
    if (updateErr) throw updateErr

    toast.success("AGIHAN GURU GANTI BERJAYA!")
    showModal.value = false
    fetchRequests()
  } catch (error) {
    toast.error("AGIHAN GAGAL: " + error.message)
  }
}

onMounted(() => {
  fetchRequests()
})
</script>