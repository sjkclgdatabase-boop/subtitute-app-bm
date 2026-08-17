<template>
  <div class="p-8 max-w-7xl mx-auto min-h-screen space-y-8 print:p-0 print:max-w-none">
    
    <!-- 顶部标题：统一的卡片风格与渐变色大标题 -->
    <div class="no-print bg-white rounded-3xl p-8 shadow-sm ring-1 ring-slate-900/5 space-y-2">
      <h1 class="text-2xl sm:text-3xl font-extrabold tracking-tight text-transparent bg-clip-text bg-gradient-to-r from-slate-900 via-indigo-800 to-violet-800">
        ANALISIS DATA AKADEMIK & PUSAT LAPORAN MMI
      </h1>
      <p class="text-slate-500 text-xs sm:text-sm font-medium leading-relaxed">
        PEMANTAUAN PELBAGAI DIMENSI TERHADAP GANGGUAN PENGAJARAN, KESAN SUBJEK DAN BEBAN GURU GANTI.
      </p>
    </div>

    <!-- 🌐 全局日期范围筛选器控制面板 -->
    <div class="no-print bg-white p-6 rounded-3xl shadow-sm ring-1 ring-slate-900/5 flex flex-wrap items-center justify-between gap-4">
      <div class="flex items-center gap-3">
        <div class="w-10 h-10 rounded-2xl bg-indigo-50 text-indigo-600 flex items-center justify-center font-bold text-lg">
          📅
        </div>
        <div>
          <div class="text-xs font-bold text-slate-400 uppercase tracking-wider">PENAPISAN JULAT TARIKH</div>
          <div class="text-xs font-extrabold text-slate-800">SEMUA LAPORAN DAN CARTA DIKIRA SECARA MASA NYATA MENGIKUT TEMPOH MASA</div>
        </div>
      </div>
      
      <div class="flex flex-wrap items-center gap-3">
        <div class="flex items-center gap-2 bg-slate-50 border border-slate-200 px-4 h-11 rounded-2xl">
          <span class="text-xs font-bold text-slate-500">DARI</span>
          <input 
            type="date" 
            v-model="startDate" 
            @change="loadAllData"
            class="bg-transparent text-xs font-bold text-slate-700 focus:outline-none cursor-pointer"
          />
        </div>
        <span class="text-slate-400 font-bold">-</span>
        <div class="flex items-center gap-2 bg-slate-50 border border-slate-200 px-4 h-11 rounded-2xl">
          <span class="text-xs font-bold text-slate-500">HINGGA</span>
          <input 
            type="date" 
            v-model="endDate" 
            @change="loadAllData"
            class="bg-transparent text-xs font-bold text-slate-700 focus:outline-none cursor-pointer"
          />
        </div>
        <button 
          @click="resetDateFilter" 
          class="px-5 h-11 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-2xl text-xs font-bold transition cursor-pointer"
        >
          TETAP SEMULA
        </button>
      </div>
    </div>

    <!-- 顶栏：分析视角切换按钮 (Tab) -->
    <div class="no-print flex flex-wrap gap-2 bg-white p-3 rounded-3xl shadow-sm ring-1 ring-slate-900/5">
      <button 
        @click="currentTab = 'overview'" 
        :class="currentTab === 'overview' ? 'bg-slate-900 text-white shadow-sm' : 'text-slate-600 hover:bg-slate-100'"
        class="px-4 py-2.5 rounded-2xl text-xs font-bold transition-all cursor-pointer"
      >
        📊 RINGKASAN & BEBAN GURU GANTI
      </button>
      <button 
        @click="currentTab = 'reason'" 
        :class="currentTab === 'reason' ? 'bg-slate-900 text-white shadow-sm' : 'text-slate-600 hover:bg-slate-100'"
        class="px-4 py-2.5 rounded-2xl text-xs font-bold transition-all cursor-pointer"
      >
        ⚠️ ANALISIS SEBAB GANGGUAN
      </button>
      <button 
        @click="currentTab = 'trend'" 
        :class="currentTab === 'trend' ? 'bg-slate-900 text-white shadow-sm' : 'text-slate-600 hover:bg-slate-100'"
        class="px-4 py-2.5 rounded-2xl text-xs font-bold transition-all cursor-pointer"
      >
        📅 KEMUNCAK TARIKH GANGGUAN
      </button>
      <button 
        @click="currentTab = 'class'" 
        :class="currentTab === 'class' ? 'bg-slate-900 text-white shadow-sm' : 'text-slate-600 hover:bg-slate-100'"
        class="px-4 py-2.5 rounded-2xl text-xs font-bold transition-all cursor-pointer"
      >
        🏫 ANALISIS KELAS TERJEJAS
      </button>
      <button 
        @click="currentTab = 'subject'" 
        :class="currentTab === 'subject' ? 'bg-slate-900 text-white shadow-sm' : 'text-slate-600 hover:bg-slate-100'"
        class="px-4 py-2.5 rounded-2xl text-xs font-bold transition-all cursor-pointer"
      >
        📚 SUBJEK TERJEJAS
      </button>
      <button 
        @click="currentTab = 'affectedTeacher'" 
        :class="currentTab === 'affectedTeacher' ? 'bg-slate-900 text-white shadow-sm' : 'text-slate-600 hover:bg-slate-100'"
        class="px-4 py-2.5 rounded-2xl text-xs font-bold transition-all cursor-pointer"
      >
        📉 GURU TERJEJAS (TOP 5)
      </button>
      <button 
        @click="currentTab = 'teacher'" 
        :class="currentTab === 'teacher' ? 'bg-slate-900 text-white shadow-sm' : 'text-slate-600 hover:bg-slate-100'"
        class="px-4 py-2.5 rounded-2xl text-xs font-bold transition-all cursor-pointer"
      >
        👨‍🏫 REKOD KESELURUHAN GURU
      </button>
    </div>

    <!-- 打印专用报表标题抬头 -->
    <div class="print-header hidden mb-6 text-center">
      <h2 class="text-2xl font-extrabold text-slate-900">LAPORAN ANALISIS DATA AKADEMIK & MMI</h2>
      <p class="text-xs text-slate-600 mt-1">
        TEMPOH MASA: [{{ startDate || 'TIADA HAD' }} HINGGA {{ endDate || 'TIADA HAD' }}]
      </p>
    </div>

    <!-- ================= TAB 1: 综合概览 ================= -->
    <div v-if="currentTab === 'overview'" class="space-y-8 animate-fadeIn">
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
        <div class="bg-white p-6 rounded-3xl shadow-sm ring-1 ring-slate-900/5">
          <div class="text-xs font-bold text-slate-400 uppercase tracking-wider">JUMLAH SLOT GANGGUAN</div>
          <div class="text-3xl font-black text-slate-900 mt-2">{{ totalInterruptionPeriods }} SLOT</div>
        </div>
        <div class="bg-white p-6 rounded-3xl shadow-sm ring-1 ring-slate-900/5">
          <div class="text-xs font-bold text-slate-400 uppercase tracking-wider">Jumlah Kes Penggantian Guru</div>
          <div class="text-3xl font-black text-indigo-600 mt-2">{{ totalSubstituteCount }} KALI</div>
        </div>
        <div class="bg-white p-6 rounded-3xl shadow-sm ring-1 ring-slate-900/5">
          <div class="text-xs font-bold text-slate-400 uppercase tracking-wider">JUMLAH REKOD GANGGUAN</div>
          <div class="text-3xl font-black text-slate-900 mt-2">{{ interruptionLogs.length }} REKOD</div>
        </div>
      </div>

      <div class="bg-white p-8 rounded-3xl shadow-sm ring-1 ring-slate-900/5">
        <div class="flex justify-between items-center mb-6">
          <h2 class="text-base font-bold text-slate-900">⚖️ KEDUDUKAN BEBAN TINGGI GURU GANTI (TOP 5)</h2>
          <button @click="exportSinglePdf" class="no-print px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-bold transition flex items-center gap-1 cursor-pointer">
            📥 CETAK / SIMPAN SEBAGAI PDF
          </button>
        </div>
        <div class="grid grid-cols-1 sm:grid-cols-5 gap-4">
          <div v-for="(t, idx) in sortedSubstituteStats.slice(0, 5)" :key="t.name" class="p-4 bg-slate-50 border border-slate-100 rounded-2xl flex flex-col justify-between">
            <div class="text-xs font-bold text-slate-400">RANK #{{ idx + 1 }}</div>
            <div class="my-2">
              <div class="text-sm font-extrabold text-slate-900">{{ t.name }}</div>
              <div class="text-[11px] text-slate-500">{{ t.subject || 'SUBJEK UMUM' }}</div>
            </div>
            <div class="text-xs font-bold text-indigo-600 bg-white px-3 py-1.5 rounded-xl shadow-sm text-center border border-slate-200">
              {{ t.count }} KALI GANTI
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- ================= TAB 2: 项目分析 ================= -->
    <div v-if="currentTab === 'reason'" class="bg-white p-8 rounded-3xl shadow-sm ring-1 ring-slate-900/5 space-y-6 animate-fadeIn">
      <div class="flex justify-between items-center">
        <div>
          <h2 class="text-lg font-bold text-slate-900">⚠️ STATISTIK SEBAB GANGGUAN PDPC</h2>
          <p class="text-xs text-slate-500 mt-1">STATISTIK SLOT MASA DAN PERATUSAN YANG DIAMBIL OLEH SETIAP AKTIVITI.</p>
        </div>
        <button @click="exportSinglePdf" class="no-print px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-bold transition flex items-center gap-1 cursor-pointer">
          📥 CETAK / SIMPAN SEBAGAI PDF
        </button>
      </div>
      <div v-if="reasonStats.length === 0" class="text-xs text-slate-400 py-12 text-center border border-dashed rounded-2xl">TIADA REKOD GANGGUAN PADA TEMPOH INI</div>
      <div v-else class="space-y-4">
        <div v-for="item in reasonStats" :key="item.reason" class="space-y-1.5 p-4 bg-slate-50 rounded-2xl">
          <div class="flex justify-between text-xs font-bold text-slate-800">
            <span>{{ item.reason }}</span>
            <span class="text-indigo-600">{{ item.count }} SLOT ({{ item.percentage }}%)</span>
          </div>
          <div class="w-full h-3 bg-slate-200 rounded-full overflow-hidden">
            <div class="h-full bg-indigo-600 rounded-full" :style="{ width: item.percentage + '%' }"></div>
          </div>
        </div>
      </div>
    </div>

    <!-- ================= TAB 3: 日期高峰 ================= -->
    <div v-if="currentTab === 'trend'" class="bg-white p-8 rounded-3xl shadow-sm ring-1 ring-slate-900/5 space-y-6 animate-fadeIn">
      <div class="flex justify-between items-center">
        <div>
          <h2 class="text-lg font-bold text-slate-900">📅 STATISTIK GANGGUAN MENGIKUT HARI</h2>
          <p class="text-xs text-slate-500 mt-1">TABURAN KEKERAPAN GANGGUAN MENGIKUT HARI DALAM SEMINGGU.</p>
        </div>
        <button @click="exportSinglePdf" class="no-print px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-bold transition flex items-center gap-1 cursor-pointer">
          📥 CETAK / SIMPAN SEBAGAI PDF
        </button>
      </div>
      <div class="grid grid-cols-1 sm:grid-cols-5 gap-4">
        <div v-for="dayData in dayOfWeekStats" :key="dayData.day" class="p-5 bg-slate-50 border border-slate-100 rounded-2xl text-center space-y-2">
          <div class="text-xs font-bold text-slate-500 uppercase">{{ dayData.day }}</div>
          <div class="text-2xl font-black text-slate-900">{{ dayData.count }} <span class="text-xs font-normal text-slate-400">SLOT</span></div>
          <div class="text-[11px] text-indigo-600 font-semibold bg-indigo-50 py-1 rounded-lg">PADA {{ dayData.percentage }}%</div>
        </div>
      </div>
    </div>

    <!-- ================= TAB 4: 班级干扰分析 ================= -->
    <div v-if="currentTab === 'class'" class="bg-white p-8 rounded-3xl shadow-sm ring-1 ring-slate-900/5 space-y-6 animate-fadeIn">
      <div class="flex justify-between items-center">
        <div>
          <h2 class="text-lg font-bold text-slate-900">🏫 STATISTIK GANGGUAN KELAS</h2>
          <p class="text-xs text-slate-500 mt-1">JUMLAH KUMULATIF SLOT MASA KELAS YANG TERJEJAS.</p>
        </div>
        <button @click="exportSinglePdf" class="no-print px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-bold transition flex items-center gap-1 cursor-pointer">
          📥 CETAK / SIMPAN SEBAGAI PDF
        </button>
      </div>

      <div class="overflow-x-auto">
        <table class="w-full text-left text-xs border-collapse print-table">
          <thead>
            <tr class="bg-slate-50 text-slate-500 uppercase tracking-wider select-none font-semibold">
              <th @click="sortClassTable('className')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                NAMA KELAS <span class="text-indigo-600">{{ classSortKey === 'className' ? (classSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
              <th @click="sortClassTable('totalPeriods')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                JUMLAH SLOT TERJEJAS <span class="text-indigo-600">{{ classSortKey === 'totalPeriods' ? (classSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
              <th @click="sortClassTable('percentage')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                PERATUSAN <span class="text-indigo-600">{{ classSortKey === 'percentage' ? (classSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-100 font-medium text-slate-800">
            <tr v-for="c in sortedClassStats" :key="c.className" class="hover:bg-slate-50">
              <td class="p-4 font-bold text-slate-900">{{ c.className }}</td>
              <td class="p-4 font-bold text-indigo-600">{{ c.totalPeriods }} SLOT</td>
              <td class="p-4 text-slate-600">{{ c.percentage }}%</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- ================= TAB 5: 被影响最多的科目排行 ================= -->
    <div v-if="currentTab === 'subject'" class="bg-white p-8 rounded-3xl shadow-sm ring-1 ring-slate-900/5 space-y-6 animate-fadeIn">
      <div class="flex justify-between items-center">
        <div>
          <h2 class="text-lg font-bold text-slate-900">📚 STATISTIK GANGGUAN SUBJEK</h2>
          <p class="text-xs text-slate-500 mt-1">KUMULATIF SLOT MASA SUBJEK YANG TERGANGGU AKIBAT CUTI ATAU AKTIVITI.</p>
        </div>
        <button @click="exportSinglePdf" class="no-print px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-bold transition flex items-center gap-1 cursor-pointer">
          📥 CETAK / SIMPAN SEBAGAI PDF
        </button>
      </div>

      <div class="overflow-x-auto">
        <table class="w-full text-left text-xs border-collapse print-table">
          <thead>
            <tr class="bg-slate-50 text-slate-500 uppercase tracking-wider select-none font-semibold">
              <th @click="sortSubjectTable('subjectName')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                NAMA SUBJEK <span class="text-indigo-600">{{ subjectSortKey === 'subjectName' ? (subjectSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
              <th @click="sortSubjectTable('totalPeriods')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                JUMLAH SLOT TERJEJAS <span class="text-indigo-600">{{ subjectSortKey === 'totalPeriods' ? (subjectSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-100 font-medium text-slate-800">
            <tr v-for="s in sortedSubjectStats" :key="s.subjectName" class="hover:bg-slate-50">
              <td class="p-4 font-bold text-slate-900">{{ s.subjectName }}</td>
              <td class="p-4 font-bold text-indigo-600">{{ s.totalPeriods }} SLOT</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- ================= TAB 6: 受影响最大老师排行榜 (Top 5) ================= -->
    <div v-if="currentTab === 'affectedTeacher'" class="bg-white p-8 rounded-3xl shadow-sm ring-1 ring-slate-900/5 space-y-6 animate-fadeIn">
      <div class="flex justify-between items-center">
        <div>
          <h2 class="text-lg font-bold text-slate-900">📉 STATISTIK GANGGUAN KELAS GURU (TOP 5)</h2>
          <p class="text-xs text-slate-500 mt-1">SENARAI 5 ORANG GURU UTAMA YANG KELASNYA PALING BANYAK TERGANGGU.</p>
        </div>
        <button @click="exportSinglePdf" class="no-print px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-bold transition flex items-center gap-1 cursor-pointer">
          📥 CETAK / SIMPAN SEBAGAI PDF
        </button>
      </div>

      <div class="overflow-x-auto">
        <table class="w-full text-left text-xs border-collapse print-table">
          <thead>
            <tr class="bg-slate-50 text-slate-500 uppercase tracking-wider select-none font-semibold">
              <th @click="sortAffectedTeacherTable('teacherName')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                NAMA GURU <span class="text-indigo-600">{{ affectedTeacherSortKey === 'teacherName' ? (affectedTeacherSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
              <th @click="sortAffectedTeacherTable('totalPeriods')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                JUMLAH SLOT TERJEJAS <span class="text-indigo-600">{{ affectedTeacherSortKey === 'totalPeriods' ? (affectedTeacherSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-100 font-medium text-slate-800">
            <tr v-for="(t, index) in sortedAffectedTeacherStats.slice(0, 5)" :key="t.teacherName" class="hover:bg-slate-50">
              <td class="p-4 font-bold text-slate-900">{{ t.teacherName }}</td>
              <td class="p-4 font-bold text-amber-600">{{ t.totalPeriods }} SLOT</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- ================= TAB 7: 老师干扰总表 ================= -->
    <div v-if="currentTab === 'teacher'" class="bg-white p-8 rounded-3xl shadow-sm ring-1 ring-slate-900/5 space-y-6 animate-fadeIn">
      <div class="flex justify-between items-center">
        <div>
          <h2 class="text-lg font-bold text-slate-900">👨‍🏫 KESELURUHAN BEBAN GANTI & GANGGUAN GURU</h2>
          <p class="text-xs text-slate-500 mt-1">PAPARAN SEMUA GURU BERDAFTAR BESERTA BEBAN GANTI DAN GANGGUAN KELAS.</p>
        </div>
        <button @click="exportSinglePdf" class="no-print px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-bold transition flex items-center gap-1 cursor-pointer">
          📥 CETAK / SIMPAN SEBAGAI PDF
        </button>
      </div>

      <div class="overflow-x-auto">
        <table class="w-full text-left text-xs border-collapse print-table">
          <thead>
            <tr class="bg-slate-50 text-slate-500 uppercase tracking-wider select-none font-semibold">
              <th @click="sortTeacherTable('name')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                NAMA GURU <span class="text-indigo-600">{{ teacherSortKey === 'name' ? (teacherSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
              <th @click="sortTeacherTable('subject')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                SUBJEK DIAJAR <span class="text-indigo-600">{{ teacherSortKey === 'subject' ? (teacherSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
              <th @click="sortTeacherTable('count')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                JUMLAH KALI GANTI <span class="text-indigo-600">{{ teacherSortKey === 'count' ? (teacherSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
              <th @click="sortTeacherTable('interruptedCount')" class="p-4 border-b cursor-pointer hover:bg-slate-100 transition">
                JUMLAH SLOT TERJEJAS <span class="text-indigo-600">{{ teacherSortKey === 'interruptedCount' ? (teacherSortAsc ? '▲' : '▼') : '↕' }}</span>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-100 font-medium text-slate-800">
            <tr v-for="stat in sortedTeacherStats" :key="stat.name" class="hover:bg-slate-50">
              <td class="p-4 font-bold text-slate-900">{{ stat.name }}</td>
              <td class="p-4 text-slate-600">{{ stat.subject || '-' }}</td>
              <td class="p-4 font-bold text-indigo-600">{{ stat.count }} SLOT</td>
              <td class="p-4 font-bold text-amber-600">{{ stat.interruptedCount }} SLOT</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, onMounted, onActivated } from 'vue'
import { supabase } from '../services/supabase'

const currentTab = ref('overview')

// 🌐 全局日期范围状态
const startDate = ref('')
const endDate = ref('')

const stats = ref([])
const interruptionLogs = ref([])
const reasonStats = ref([])
const dayOfWeekStats = ref([])
const classStats = ref([])
const subjectStats = ref([])
const affectedTeacherStats = ref([])

// 通用安全排序方法
const smartSort = (valA, valB, asc) => {
  const a = valA ?? '';
  const b = valB ?? '';
  
  if (typeof a === 'number' && typeof b === 'number') {
    return asc ? a - b : b - a
  }
  const strA = String(a).toLowerCase();
  const strB = String(b).toLowerCase();
  return asc ? strA.localeCompare(strB) : strB.localeCompare(strA)
}

// 排序状态与方法
const classSortKey = ref('totalPeriods')
const classSortAsc = ref(false)
const sortClassTable = (key) => {
  if (classSortKey.value === key) classSortAsc.value = !classSortAsc.value
  else { classSortKey.value = key; classSortAsc.value = true }
}
const sortedClassStats = computed(() => [...classStats.value].sort((a, b) => smartSort(a[classSortKey.value], b[classSortKey.value], classSortAsc.value)))

const subjectSortKey = ref('totalPeriods')
const subjectSortAsc = ref(false)
const sortSubjectTable = (key) => {
  if (subjectSortKey.value === key) subjectSortAsc.value = !subjectSortAsc.value
  else { subjectSortKey.value = key; subjectSortAsc.value = true }
}
const sortedSubjectStats = computed(() => [...subjectStats.value].sort((a, b) => smartSort(a[subjectSortKey.value], b[subjectSortKey.value], subjectSortAsc.value)))

const affectedTeacherSortKey = ref('totalPeriods')
const affectedTeacherSortAsc = ref(false)
const sortAffectedTeacherTable = (key) => {
  if (affectedTeacherSortKey.value === key) affectedTeacherSortAsc.value = !affectedTeacherSortAsc.value
  else { affectedTeacherSortKey.value = key; affectedTeacherSortAsc.value = true }
}
const sortedAffectedTeacherStats = computed(() => [...affectedTeacherStats.value].sort((a, b) => smartSort(a[affectedTeacherSortKey.value], b[affectedTeacherSortKey.value], affectedTeacherSortAsc.value)))

const teacherSortKey = ref('count')
const teacherSortAsc = ref(false)
const sortTeacherTable = (key) => {
  if (teacherSortKey.value === key) teacherSortAsc.value = !teacherSortAsc.value
  else { teacherSortKey.value = key; teacherSortAsc.value = true }
}
const sortedTeacherStats = computed(() => [...stats.value].sort((a, b) => smartSort(a[teacherSortKey.value], b[teacherSortKey.value], teacherSortAsc.value)))

// 其他常规统计
const totalSubstituteCount = computed(() => stats.value.reduce((acc, cur) => acc + (cur.count || 0), 0))
const totalInterruptionPeriods = computed(() => interruptionLogs.value.reduce((acc, cur) => acc + ((cur.end_period || 0) - (cur.start_period || 0) + 1), 0))
const sortedSubstituteStats = computed(() => [...stats.value].sort((a, b) => (b.count || 0) - (a.count || 0)))

// 重置日期选择
const resetDateFilter = () => {
  startDate.value = ''
  endDate.value = ''
  loadAllData()
}

// 🔄 数据加载函数（双表融合精准版）
const loadAllData = async () => {
  const { data: teachers } = await supabase.from('teachers').select('*')
  
  // 1. 获取代课记录（为了剔除 swap 换课）
  let assignQuery = supabase
    .from('substitute_assignments')
    .select('sub_teacher_id, assignment_type, leave_request_id, leave_requests!inner(leave_date)')

  if (startDate.value) assignQuery = assignQuery.gte('leave_requests.leave_date', startDate.value)
  if (endDate.value) assignQuery = assignQuery.lte('leave_requests.leave_date', endDate.value)

  const { data: assignments } = await assignQuery

  // 将所有换课(swap)的 leave_request_id 存起来
  const swapLeaveIds = new Set()
  assignments?.forEach(a => {
    if (a.assignment_type === 'swap' && a.leave_request_id) {
      swapLeaveIds.add(a.leave_request_id)
    }
  })

  // 2. 获取 MMI 记录
  let mmiQuery = supabase.from('mmi_interruptions').select('*')
  if (startDate.value) mmiQuery = mmiQuery.gte('interruption_date', startDate.value)
  if (endDate.value) mmiQuery = mmiQuery.lte('interruption_date', endDate.value)
  const { data: mmiData } = await mmiQuery

  if (mmiData) interruptionLogs.value = mmiData

  // 3. 获取这段时间内的精准请假记录 (leave_requests)
  let leaveQuery = supabase.from('leave_requests').select('*')
  if (startDate.value) leaveQuery = leaveQuery.gte('leave_date', startDate.value)
  if (endDate.value) leaveQuery = leaveQuery.lte('leave_date', endDate.value)
  const { data: leaveData } = await leaveQuery

  // ================== 开始统计 ==================
  const teacherMap = {}
  const teacherNameSet = new Set()
  
  teachers?.forEach(t => {
    teacherMap[t.id] = { name: t.name, subject: t.subject, count: 0, interruptedCount: 0 }
    teacherNameSet.add(t.name.trim().toUpperCase())
  })

  // 教师代课量统计 (过滤 swap)
  assignments?.forEach(a => {
    if (a.assignment_type !== 'swap' && a.sub_teacher_id && teacherMap[a.sub_teacher_id]) {
      teacherMap[a.sub_teacher_id].count++
    }
  })

  // 教师受干扰量统计 (基于 MMI)
  const teacherInterruptionMap = {}
  mmiData?.forEach(l => {
    let rawTarget = (l.target_display || '').trim()
    let tName = ''
    if (rawTarget.includes('GURU:')) tName = rawTarget.replace('GURU:', '').trim()
    else if (rawTarget.includes('教师:')) tName = rawTarget.replace('教师:', '').trim()
    else if (teacherNameSet.has(rawTarget.toUpperCase())) tName = rawTarget

    if (tName) {
      const pCount = (l.end_period || 0) - (l.start_period || 0) + 1
      teacherInterruptionMap[tName.toUpperCase()] = (teacherInterruptionMap[tName.toUpperCase()] || 0) + pCount
    }
  })

  stats.value = Object.values(teacherMap).map(t => ({
    ...t,
    interruptedCount: teacherInterruptionMap[t.name.trim().toUpperCase()] || 0
  }))

  // MMI 项目占比分析与高峰日期
  if (mmiData) {
    const totalPAll = mmiData.reduce((acc, cur) => acc + ((cur.end_period || 0) - (cur.start_period || 0) + 1), 0)
    const reasons = {}
    mmiData.forEach(l => { const pCount = (l.end_period || 0) - (l.start_period || 0) + 1; reasons[l.reason] = (reasons[l.reason] || 0) + pCount })
    reasonStats.value = Object.entries(reasons).map(([reason, count]) => ({ reason, count, percentage: totalPAll > 0 ? ((count / totalPAll) * 100).toFixed(1) : 0 })).sort((a, b) => b.count - a.count)

    const dayNames = { 1: 'ISNIN', 2: 'SELASA', 3: 'RABU', 4: 'KHAMIS', 5: 'JUMAAT', 6: 'SABTU', 7: 'AHAD' }
    const daysCount = {}
    mmiData.forEach(l => { const dIndex = new Date(l.interruption_date).getDay() || 7; const dName = dayNames[dIndex] || 'LAIN-LAIN'; const pCount = (l.end_period || 0) - (l.start_period || 0) + 1; daysCount[dName] = (daysCount[dName] || 0) + pCount })
    dayOfWeekStats.value = ['ISNIN', 'SELASA', 'RABU', 'KHAMIS', 'JUMAAT'].map(day => ({ day, count: daysCount[day] || 0, percentage: totalPAll > 0 ? (((daysCount[day] || 0) / totalPAll) * 100).toFixed(1) : 0 }))
  }

  // ================== 核心升级：双表融合计算班级和科目干扰 ==================
  const classMap = {}
  const subjectMap = {}
  let totalClassPeriods = 0

  // 步骤A：融入单纯的班级活动 (来自 MMI 表)
  mmiData?.forEach(l => { 
    let rawTarget = (l.target_display || '').trim(); 
    if (rawTarget.includes('GURU') || rawTarget.includes('教师') || teacherNameSet.has(rawTarget.toUpperCase())) return; 
    
    const pCount = (l.end_period || 0) - (l.start_period || 0) + 1; 
    
    if (rawTarget.startsWith('KELAS:') || rawTarget.startsWith('班级:')) {
      const cNames = rawTarget.replace(/(?:KELAS|班级)[:：]/, '').split(',');
      cNames.forEach(c => {
        const cleanC = c.trim();
        if (cleanC) {
          classMap[cleanC] = (classMap[cleanC] || 0) + pCount;
          totalClassPeriods += pCount;
        }
      });
    } else {
      const cName = rawTarget || 'SEMUA KELAS'; 
      classMap[cName] = (classMap[cName] || 0) + pCount; 
      totalClassPeriods += pCount;
    }
  })

  // 步骤B：融入教师请假对班级的冲击 (来自 leave_requests 表)
  leaveData?.forEach(req => {
    if (swapLeaveIds.has(req.id)) return;

    const cNames = req.class_name ? req.class_name.split('/') : ['KELAS TIDAK DIKETAHUI'];
    cNames.forEach(c => {
      const cleanName = c.trim();
      if (cleanName) {
        classMap[cleanName] = (classMap[cleanName] || 0) + 1; 
        totalClassPeriods += 1;
      }
    })

    const sub = req.subject ? req.subject.trim() : 'SUBJEK TIDAK DIKETAHUI';
    if (sub && sub !== 'SUBJEK TIDAK DIKETAHUI') {
      subjectMap[sub] = (subjectMap[sub] || 0) + 1;
    }
  })

  classStats.value = Object.entries(classMap)
    .map(([className, totalPeriods]) => ({ 
      className, 
      totalPeriods, 
      percentage: totalClassPeriods > 0 ? ((totalPeriods / totalClassPeriods) * 100).toFixed(1) : 0 
    }))
    .sort((a, b) => b.totalPeriods - a.totalPeriods)

  subjectStats.value = Object.entries(subjectMap)
    .map(([subjectName, totalPeriods]) => ({ subjectName, totalPeriods }))
    .sort((a, b) => b.totalPeriods - a.totalPeriods)
  
  affectedTeacherStats.value = stats.value.filter(t => t.interruptedCount > 0).map(t => ({ teacherName: t.name, totalPeriods: t.interruptedCount })).sort((a, b) => b.totalPeriods - a.totalPeriods)
}

onMounted(loadAllData)

onActivated(() => {
  loadAllData()
})

// 🖨️ 默认强制 A4 纵向 (Portrait) 打印
const exportSinglePdf = () => {
  window.print()
}
</script>

<style scoped>
.print-header {
  display: none;
}

@media print {
  @page {
    size: portrait; /* 强制锁定为竖版纵向 */
    margin: 10mm;
  }
  .no-print {
    display: none !important;
  }
  .print-header {
    display: block !important;
    margin-bottom: 20px;
  }
  body, * {
    font-family: "Microsoft YaHei", "PingFang SC", "Heiti SC", "WenQuanYi Micro Hei", Arial, sans-serif !important;
    background: white !important;
    color: black !important;
    -webkit-print-color-adjust: exact;
    print-color-adjust: exact;
  }
  .print-table {
    width: 100% !important;
    border-collapse: collapse !important;
    font-size: 11px !important;
  }
  .print-table th, .print-table td {
    border: 1px solid #cbd5e1 !important;
    padding: 8px 10px !important;
  }
  .print-table th {
    background-color: #f1f5f9 !important;
    color: #0f172a !important;
  }
}
</style>