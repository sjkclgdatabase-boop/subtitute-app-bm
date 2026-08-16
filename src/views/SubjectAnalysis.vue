<template>
  <div class="p-8 max-w-7xl mx-auto min-h-screen space-y-8">
    
    <!-- 头部区域 -->
    <div class="mb-2 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
      <div>
        <h1 class="text-3xl font-extrabold tracking-tight text-transparent bg-clip-text bg-gradient-to-r from-slate-900 via-indigo-800 to-violet-800">
          PUSAT ANALISIS SASARAN & KADAR PENCAPAIAN SUBJEK
        </h1>
        <p class="text-slate-500 text-sm mt-2 font-medium">
          MENJEJAK SASARAN PENGAJARAN, GANGGUAN MMI DAN PENCAPAIAN DARI PELBAGAI DIMENSI, SOKONG LAPORAN DATA, PAPAN PEMUKA VISUAL & EKSPORT PDF.
        </p>
      </div>

      <!-- 顶部操作栏 -->
      <div class="flex items-center gap-3">
        <button @click="exportPdfReport" class="no-print bg-emerald-600 hover:bg-emerald-700 text-white px-4 py-2.5 rounded-xl text-xs font-bold transition shadow-md flex items-center gap-2 cursor-pointer">
          <span>📥 CETAK / SIMPAN SEBAGAI PDF</span>
        </button>
        <button @click="showManageModal = true" class="no-print bg-slate-900 hover:bg-slate-800 text-white px-4 py-2.5 rounded-xl text-xs font-bold transition shadow-sm cursor-pointer">
          ⚙️ URUS SASARAN
        </button>
        <button @click="loadAnalyticsData" :disabled="loading" class="no-print bg-indigo-600 hover:bg-indigo-700 text-white px-5 py-2.5 rounded-xl text-xs font-bold transition shadow-md cursor-pointer">
          {{ loading ? 'SEDANG MENGIRA...' : '🔄 SEGAR SEMULA DATA' }}
        </button>
      </div>
    </div>

    <!-- 🗂️ 视图切换 Tab -->
    <div class="no-print flex border-b border-slate-200 gap-8">
      <button 
        @click="activeTab = 'table'" 
        class="pb-4 text-sm font-bold transition-all relative flex items-center gap-2 cursor-pointer"
        :class="activeTab === 'table' ? 'text-indigo-600 border-b-2 border-indigo-600' : 'text-slate-400 hover:text-slate-700'"
      >
        <span>📊 LAPORAN DATA TERPERINCI</span>
      </button>
      <button 
        @click="activeTab = 'chart'" 
        class="pb-4 text-sm font-bold transition-all relative flex items-center gap-2 cursor-pointer"
        :class="activeTab === 'chart' ? 'text-indigo-600 border-b-2 border-indigo-600' : 'text-slate-400 hover:text-slate-700'"
      >
        <span>📈 PAPAN PEMUKA CARTA VISUAL</span>
      </button>
    </div>

    <!-- 🔍 多维度高级筛选面板 -->
    <div class="no-print bg-white p-6 rounded-3xl border border-slate-200 shadow-sm grid grid-cols-1 sm:grid-cols-4 gap-4">
      <div>
        <label class="block text-xs font-bold text-slate-500 mb-2">PENAPIS MENGIKUT TAHUN</label>
        <select v-model="filterGrade" @change="onGradeChange" class="w-full bg-slate-50 border border-slate-200 px-4 py-2.5 rounded-xl text-xs font-semibold text-slate-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 cursor-pointer">
          <option value="all">SEMUA TAHUN SEKOLAH</option>
          <option v-for="g in [1,2,3,4,5,6]" :key="g" :value="g">TAHUN {{ g }}</option>
        </select>
      </div>

      <div>
        <label class="block text-xs font-bold text-slate-500 mb-2">PENAPIS MENGIKUT KELAS</label>
        <select v-model="filterClass" class="w-full bg-slate-50 border border-slate-200 px-4 py-2.5 rounded-xl text-xs font-semibold text-slate-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 cursor-pointer">
          <option value="all">SEMUA KELAS UNTUK TAHUN INI</option>
          <option v-for="c in availableClasses" :key="c.id" :value="c.class_name">{{ c.class_name }}</option>
        </select>
      </div>

      <div>
        <label class="block text-xs font-bold text-slate-500 mb-2">PENAPIS MENGIKUT SUBJEK</label>
        <select v-model="filterSubject" class="w-full bg-slate-50 border border-slate-200 px-4 py-2.5 rounded-xl text-xs font-semibold text-slate-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 cursor-pointer">
          <option value="all">SEMUA SUBJEK</option>
          <option v-for="s in uniqueSubjects" :key="s" :value="s">{{ s }}</option>
        </select>
      </div>

      <div>
        <label class="block text-xs font-bold text-indigo-600 mb-2">👨‍🏫 PENAPIS MENGIKUT GURU SUBJEK</label>
        <select v-model="filterTeacher" class="w-full bg-indigo-50/50 border border-indigo-200 px-4 py-2.5 rounded-xl text-xs font-semibold text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500 cursor-pointer">
          <option value="all">SEMUA GURU SEKOLAH</option>
          <option v-for="tch in allTeachers" :key="tch.id" :value="tch.name">{{ tch.name }}</option>
        </select>
      </div>
    </div>

    <!-- 🎯 核心统计指标卡片容器 -->
    <div id="pdfContentContainer" class="space-y-8 bg-white p-4 rounded-3xl">
      
      <!-- 打印专属标题抬头 -->
      <div class="print-header bg-slate-50 p-6 rounded-2xl border border-slate-200 mb-4 text-center">
        <h2 class="text-xl font-extrabold text-slate-950">LAPORAN PENILAIAN SASARAN PENGAJARAN SEKOLAH & GANGGUAN MMI</h2>
        <p class="text-xs text-slate-600 mt-1">
          SYARAT PENAPISAN: TAHUN [{{ filterGrade === 'all' ? 'SEMUA' : filterGrade }}] | KELAS [{{ filterClass === 'all' ? 'SEMUA' : filterClass }}] | SUBJEK [{{ filterSubject === 'all' ? 'SEMUA' : filterSubject }}] | GURU [{{ filterTeacher === 'all' ? 'SEMUA' : filterTeacher }}]
        </p>
      </div>

      <div class="grid grid-cols-1 sm:grid-cols-3 gap-6 print-summary">
        <div class="bg-white p-6 rounded-3xl border border-slate-200/80 shadow-sm">
          <div class="text-xs font-bold text-slate-500 uppercase tracking-wider">JUMLAH KEPUTUSAN PENAPISAN</div>
          <div class="text-3xl font-black text-slate-900 mt-2">{{ analysisSummary.total }}</div>
        </div>
        <div class="bg-white p-6 rounded-3xl border border-slate-200/80 shadow-sm">
          <div class="text-xs font-bold text-emerald-600 uppercase tracking-wider">SASARAN DICAPAI (MELEPASI)</div>
          <div class="text-3xl font-black text-emerald-700 mt-2">{{ analysisSummary.met }}</div>
        </div>
        <div class="bg-white p-6 rounded-3xl border border-slate-200/80 shadow-sm">
          <div class="text-xs font-bold text-red-600 uppercase tracking-wider">⚠️ BELUM DICAPAI (AMARAN)</div>
          <div class="text-3xl font-black text-red-700 mt-2">{{ analysisSummary.unmet }}</div>
        </div>
      </div>

      <!-- ================= TAB 1: 详细数据报表视图 ================= -->
      <div v-if="activeTab === 'table'" class="bg-white rounded-3xl shadow-sm ring-1 ring-slate-900/5 overflow-hidden">
        <div class="p-6 border-b border-slate-100 flex justify-between items-center">
          <h3 class="text-xs font-bold uppercase tracking-wider text-slate-500">LAPORAN ANALISIS TERPERINCI (PERBANDINGAN KELAS, SUBJEK & GURU)</h3>
          <span class="text-[11px] text-slate-500">NISBAH KALENDAR PERSEKOLAHAN BERKESAN: {{ (progressRatio * 100).toFixed(1) }}%</span>
        </div>

        <div>
          <table class="w-full text-left border-collapse print-table">
            <thead>
              <tr class="bg-slate-100 text-[11px] font-bold text-slate-700 uppercase tracking-wider border-b border-slate-300">
                <th @click="toggleSort('class_name')" class="py-3 px-4 cursor-pointer hover:bg-slate-200 transition select-none">
                  TAHUN / KELAS <span class="text-indigo-600">{{ getSortIcon('class_name') }}</span>
                </th>
                <th @click="toggleSort('subject_name')" class="py-3 px-4 cursor-pointer hover:bg-slate-200 transition select-none">
                  NAMA SUBJEK <span class="text-indigo-600">{{ getSortIcon('subject_name') }}</span>
                </th>
                <th @click="toggleSort('teacher_name')" class="py-3 px-4 cursor-pointer hover:bg-slate-200 transition select-none">
                  GURU SUBJEK <span class="text-indigo-600">{{ getSortIcon('teacher_name') }}</span>
                </th>
                <th @click="toggleSort('target')" class="py-3 px-4 text-center cursor-pointer hover:bg-slate-200 transition select-none">
                  SASARAN RANCANGAN TAHUNAN <span class="text-indigo-600">{{ getSortIcon('target') }}</span>
                </th>
                <th @click="toggleSort('expected')" class="py-3 px-4 text-center cursor-pointer hover:bg-slate-200 transition select-none">
                  KEMAJUAN SEBENAR TEORETIKAL <span class="text-indigo-600">{{ getSortIcon('expected') }}</span>
                </th>
                <th @click="toggleSort('lostCount')" class="py-3 px-4 text-center cursor-pointer hover:bg-slate-200 transition select-none">
                  Jumlah Waktu PdPc terjejas AKIBAT GANGGUAN <span class="text-indigo-600">{{ getSortIcon('lostCount') }}</span>
                </th>
                <th @click="toggleSort('actual')" class="py-3 px-4 text-center cursor-pointer hover:bg-slate-200 transition select-none">
                  PELAKSANAAN SEBENAR <span class="text-indigo-600">{{ getSortIcon('actual') }}</span>
                </th>
                <th @click="toggleSort('gap')" class="py-3 px-4 text-center cursor-pointer hover:bg-slate-200 transition select-none">
                  JURANG <span class="text-indigo-600">{{ getSortIcon('gap') }}</span>
                </th>
                <th @click="toggleSort('status')" class="py-3 px-4 text-center cursor-pointer hover:bg-slate-200 transition select-none">
                  STATUS <span class="text-indigo-600">{{ getSortIcon('status') }}</span>
                </th>
              </tr>
            </thead>
            <tbody class="divide-y divide-slate-200 text-xs font-medium text-slate-800">
              <tr v-if="filteredAnalysisList.length === 0">
                <td colspan="9" class="py-12 text-center text-slate-500">
                  TIADA REKOD ANALISIS DIJUMPAI, SILA LARASKAN SYARAT PENAPISAN.
                </td>
              </tr>
              <tr v-for="(item, idx) in filteredAnalysisList" :key="idx" class="hover:bg-slate-50">
                <td class="py-3 px-4 font-bold text-slate-900">
                  TAHUN {{ item.grade }} - {{ item.class_name }}
                </td>
                <td class="py-3 px-4 font-bold text-slate-900">{{ item.subject_name }}</td>
                <td class="py-3 px-4 font-bold text-indigo-900">{{ item.teacher_name || 'BELUM DITETAPKAN' }}</td>
                <td class="py-3 px-4 text-center font-bold">{{ item.target }} SLOT</td>
                <td class="py-3 px-4 text-center font-bold text-indigo-700">{{ item.expected }} SLOT</td>
                <td class="py-3 px-4 text-center text-amber-700 font-bold">-{{ item.lostCount }} SLOT</td>
                <td class="py-3 px-4 text-center font-bold text-slate-900">{{ item.actual }} SLOT</td>
                <td class="py-3 px-4 text-center font-bold" :class="item.gap >= 0 ? 'text-emerald-700' : 'text-red-700'">
                  {{ item.gap >= 0 ? '+' + item.gap : item.gap }} SLOT
                </td>
                <td class="py-3 px-4 text-center font-bold">
                  <span 
                    class="px-2.5 py-1 rounded-lg text-xs"
                    :class="{
                      'bg-red-50 text-red-700': item.status === 'BELUM MENCAPAI SASARAN',
                      'bg-emerald-50 text-emerald-700': item.status === 'MELEPASI SASARAN',
                      'bg-slate-100 text-slate-700': item.status === 'MENCAPAI SASARAN'
                    }"
                  >
                    {{ item.status }}
                  </span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- ================= TAB 2: 📈 视觉图表分析看板 ================= -->
      <div v-if="activeTab === 'chart'" class="space-y-6">
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
            <div class="flex justify-between items-center">
              <h3 class="text-sm font-bold text-slate-800">🎯 KADAR KESIHATAN PENCAPAIAN KESELURUHAN SUBJEK</h3>
              <span class="text-xs text-indigo-600 font-bold bg-indigo-50 px-2.5 py-1 rounded-lg">PENGIRAAN MASA NYATA</span>
            </div>
            
            <div class="py-4 flex flex-col items-center justify-center space-y-4">
              <div class="relative w-36 h-36 rounded-full flex items-center justify-center border-8 border-slate-100 shadow-inner"
                   :style="{ borderColor: completionRate >= 80 ? '#10b981' : '#f59e0b' }">
                <div class="text-center">
                  <span class="text-3xl font-black text-slate-900">{{ completionRate }}%</span>
                  <div class="text-[10px] text-slate-400 font-bold uppercase mt-1">KADAR PENCAPAIAN</div>
                </div>
              </div>
              <p class="text-xs text-slate-500 font-medium">DI BAWAH SYARAT SEMASA, <strong class="text-slate-800">{{ analysisSummary.met }}</strong> SUBJEK TELAH MENCAPAI SASARAN, <strong class="text-red-600">{{ analysisSummary.unmet }}</strong> BELUM MENCAPAI.</p>
            </div>
          </div>

          <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
            <div class="flex justify-between items-center">
              <h3 class="text-sm font-bold text-slate-800">📊 TABURAN PENCAPAIAN MENGIKUT TAHUN</h3>
              <span class="text-xs text-slate-400">DICAPAI VS BELUM DICAPAI</span>
            </div>

            <div class="space-y-3 pt-2">
              <div v-for="g in [1,2,3,4,5,6]" :key="g" class="space-y-1">
                <div class="flex justify-between text-xs font-bold">
                  <span class="text-slate-700">TAHUN {{ g }} TAHUN</span>
                  <span class="text-slate-500">DICAPAI: {{ getGradeStats(g).met }} / BELUM DICAPAI: {{ getGradeStats(g).unmet }}</span>
                </div>
                <div class="w-full h-3 bg-slate-100 rounded-full overflow-hidden flex">
                  <div class="bg-emerald-500 h-full transition-all duration-500" :style="{ width: getGradeStats(g).total > 0 ? (getGradeStats(g).met / getGradeStats(g).total) * 100 + '%' : '0%' }"></div>
                  <div class="bg-red-400 h-full transition-all duration-500" :style="{ width: getGradeStats(g).total > 0 ? (getGradeStats(g).unmet / getGradeStats(g).total) * 100 + '%' : '0%' }"></div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-6">
          <div class="flex justify-between items-center">
            <div>
              <h3 class="text-sm font-bold text-slate-800">⚠️ KEDUDUKAN SUBJEK KEHILANGAN SLOT TERBANYAK AKIBAT GANGGUAN MMI</h3>
              <p class="text-xs text-slate-400 mt-0.5">MENUNJUKKAN KEHILANGAN SLOT PENGAJARAN MENGIKUT SUBJEK AKIBAT CUTI ATAU URUSAN RASMI</p>
            </div>
            <span class="text-xs bg-amber-50 text-amber-700 px-3 py-1 rounded-full font-bold">AMARAN GANGGUAN</span>
          </div>

          <div class="space-y-4">
            <div v-if="subjectLossRanking.length === 0" class="text-xs text-slate-400 text-center py-6">
              TIADA REKOD KEHILANGAN GANGGUAN DI BAWAH SYARAT SEMASA.
            </div>
            <div v-for="(item, index) in subjectLossRanking.slice(0, 5)" :key="index" class="space-y-1.5">
              <div class="flex justify-between items-center text-xs font-bold">
                <span class="text-slate-800 flex items-center gap-2">
                  <span class="w-5 h-5 rounded-full bg-slate-100 text-slate-600 flex items-center justify-center text-[10px] font-black">{{ index + 1 }}</span>
                  {{ item.subject }}
                </span>
                <span class="text-amber-600 font-black">-{{ item.lost }} JUMLAH KEHILANGAN SLOT</span>
              </div>
              <div class="w-full h-2.5 bg-slate-100 rounded-full overflow-hidden">
                <div class="bg-amber-500 h-full rounded-full transition-all duration-500" 
                     :style="{ width: Math.min(100, (item.lost / maxSubjectLoss) * 100) + '%' }"></div>
              </div>
            </div>
          </div>
        </div>

      </div>
    </div>

    <!-- 弹窗：管理目标 -->
    <div v-if="showManageModal" class="fixed inset-0 z-50 flex items-center justify-center bg-slate-900/50 backdrop-blur-sm p-4">
      <div class="bg-white rounded-3xl max-w-3xl w-full p-6 shadow-2xl space-y-6 max-h-[90vh] overflow-y-auto">
        <div class="flex justify-between items-center border-b border-slate-100 pb-4">
          <h3 class="text-base font-bold text-slate-800">⚙️ PENYENGGARAN SASARAN SUBJEK DALAM TALIAN</h3>
          <button @click="showManageModal = false" class="text-slate-400 hover:text-slate-700 font-bold text-sm cursor-pointer">✕ TUTUP</button>
        </div>

        <div class="bg-slate-50 p-4 rounded-2xl border border-slate-200 grid grid-cols-1 sm:grid-cols-4 gap-3 items-end">
          <div>
            <label class="block text-[11px] font-bold text-slate-600 mb-1">TAHUN</label>
            <select v-model="newTarget.grade" class="w-full bg-white border border-slate-200 px-3 py-2 rounded-xl text-xs font-semibold cursor-pointer">
              <option v-for="g in [1,2,3,4,5,6]" :key="g" :value="g">TAHUN {{ g }}</option>
            </select>
          </div>
          <div>
            <label class="block text-[11px] font-bold text-slate-600 mb-1">NAMA SUBJEK</label>
            <input type="text" v-model="newTarget.subject_name" placeholder="CONTOH: MATEMATIK" class="w-full bg-white border border-slate-200 px-3 py-2 rounded-xl text-xs font-semibold" />
          </div>
          <div>
            <label class="block text-[11px] font-bold text-slate-600 mb-1">JUMLAH SLOT RANCANGAN</label>
            <input type="number" v-model.number="newTarget.planned_periods" placeholder="CONTOH: 215" class="w-full bg-white border border-slate-200 px-3 py-2 rounded-xl text-xs font-semibold" />
          </div>
          <button @click="addTarget" class="bg-indigo-600 hover:bg-indigo-700 text-white py-2 rounded-xl text-xs font-bold shadow-sm transition cursor-pointer">
            ➕ TAMBAH SASARAN BAHARU
          </button>
        </div>

        <div class="space-y-3">
          <h4 class="text-xs font-bold text-slate-500 uppercase tracking-wider">SENARAI SASARAN SUBJEK YANG DITETAPKAN</h4>
          <div class="divide-y divide-slate-100 border border-slate-200 rounded-2xl overflow-hidden max-h-60 overflow-y-auto">
            <div v-for="t in allTargets" :key="t.id" class="p-3 flex items-center justify-between bg-white hover:bg-slate-50 transition">
              <div class="flex items-center gap-4 text-xs font-semibold text-slate-700">
                <span class="px-2.5 py-1 bg-indigo-50 text-indigo-700 rounded-lg font-bold">TAHUN {{ t.grade }}</span>
                <span class="text-slate-900 font-bold">{{ t.subject_name }}</span>
                <span class="text-slate-500">SASARAN: <strong>{{ t.planned_periods }}</strong> SLOT</span>
              </div>
              <button @click="deleteTarget(t.id)" class="text-slate-400 hover:text-red-600 text-xs font-bold px-2 py-1 transition cursor-pointer">
                🗑️ PADAM
              </button>
            </div>
          </div>
        </div>

        <div class="flex justify-end pt-2">
          <button @click="loadAnalyticsData(); showManageModal = false" class="bg-slate-900 hover:bg-slate-800 text-white px-5 py-2 rounded-xl text-xs font-bold shadow-sm cursor-pointer">
            SELESAI & SEGAR SEMULA LAPORAN
          </button>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, onMounted, onActivated } from 'vue'
import { supabase } from '../services/supabase'
import { useToast } from '../utils/toast'

const toast = useToast()
const loading = ref(false)
const activeTab = ref('table')

const filterGrade = ref('all')
const filterClass = ref('all')
const filterSubject = ref('all')
const filterTeacher = ref('all')

const sortKey = ref('grade')
const sortOrder = ref('asc')

const analysisList = ref([])
const allClasses = ref([])
const allTargets = ref([])
const allSchoolWeeks = ref([])
const allTeachers = ref([])

const showManageModal = ref(false)
const newTarget = ref({ grade: 1, subject_name: '', planned_periods: 215, kpm_min_hours: 180 })

const progressRatio = computed(() => {
  if (!allSchoolWeeks.value || allSchoolWeeks.value.length === 0) return 0.5
  const totalSchoolWeeks = allSchoolWeeks.value.filter(w => w.is_school_week).length
  if (totalSchoolWeeks === 0) return 0.5
  const today = new Date().toISOString().split('T')[0]
  const passedSchoolWeeks = allSchoolWeeks.value.filter(w => {
    return w.is_school_week && w.end_date <= today
  }).length
  const ratio = passedSchoolWeeks / totalSchoolWeeks
  return Math.min(1, Math.max(0, ratio))
})

const cleanString = (str) => {
  if (!str) return ''
  return String(str).trim().toUpperCase().replace(/[^A-Z0-9\u4e00-\u9fa5]/g, '')
}

const standardizeSubjectName = (name) => {
  const clean = cleanString(name)
  if (!clean) return ''
  
  if (['BI', 'ENGLISH', 'BAHASAINGGERIS', 'ENG', '英文'].includes(clean)) return 'BAHASA INGGERIS'
  if (['BM', 'MELAYU', 'BAHASAMELAYU', 'MALAY', '国文', '马来文'].includes(clean)) return 'BAHASA MELAYU'
  if (['BC', 'CINA', 'BAHASACINA', 'CHINESE', '华文', '华语'].includes(clean)) return 'BAHASA CINA'
  
  if (['MATEMATIK', 'MATH', 'MT', 'MM', '数学'].includes(clean)) return 'MATEMATIK'
  if (['SN', 'SAINS', 'SCIENCE', 'SC', '科学'].includes(clean)) return 'SAINS'
  
  if (['PJ', 'PENDIDIKANJASMANI', 'JASMANI', 'PE', '体育'].includes(clean)) return 'PENDIDIKAN JASMANI'
  if (['PM', 'PENDIDIKANMORAL', 'MORAL', '道德'].includes(clean)) return 'PENDIDIKAN MORAL'
  if (['PI', 'PENDIDIKANISLAM', 'ISLAM', '宗教'].includes(clean)) return 'PENDIDIKAN ISLAM'
  if (['PSV', 'PENDIDIKANSENIVISUAL', 'SENI', 'VISUAL', 'ART', '美术'].includes(clean)) return 'PENDIDIKAN SENI VISUAL'
  if (['MZ', 'PMUZIK', 'PENDIDIKANMUZIK', 'MUZIK', 'MUSIC', '音乐'].includes(clean)) return 'PENDIDIKAN MUZIK'
  if (['PK', 'PENDIDIKANKESIHATAN', 'KESIHATAN', 'HEALTH', '健教', '健康教育'].includes(clean)) return 'PENDIDIKAN KESIHATAN'
  if (['SEJARAH', 'SEJ', 'HIST', '历史'].includes(clean)) return 'SEJARAH'
  if (['RBT', 'REKABENTUKDANTEKNOLOGI', 'REKABENTUK', '设计与工艺'].includes(clean)) return 'REKA BENTUK DAN TEKNOLOGI'
  
  return clean
}

const isSubjectMatch = (subjA, subjB) => {
  if (!subjA || !subjB) return false
  const stdA = standardizeSubjectName(subjA)
  const stdB = standardizeSubjectName(subjB)
  if (stdA && stdB && stdA === stdB) return true
  
  const cA = cleanString(subjA)
  const cB = cleanString(subjB)
  return cA === cB || cA.includes(cB) || cB.includes(cA)
}

const toggleSort = (key) => {
  if (sortKey.value === key) {
    sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc'
  } else {
    sortKey.value = key
    sortOrder.value = 'asc'
  }
}

const getSortIcon = (key) => {
  if (sortKey.value !== key) return '↕'
  return sortOrder.value === 'asc' ? '▲' : '▼'
}

const onGradeChange = () => {
  filterClass.value = 'all'
}

const availableClasses = computed(() => {
  if (filterGrade.value === 'all') return allClasses.value
  return allClasses.value.filter(c => Number(c.grade) === Number(filterGrade.value))
})

const uniqueSubjects = computed(() => {
  const subs = new Set(allTargets.value.map(t => standardizeSubjectName(t.subject_name)))
  return Array.from(subs)
})

const fetchAllRows = async (tableName) => {
  let allData = []
  let from = 0
  const limit = 1000 
  
  while (true) {
    const { data, error } = await supabase
      .from(tableName)
      .select('*')
      .range(from, from + limit - 1)
      
    if (error) throw error
    if (data) allData.push(...data)
    
    if (!data || data.length < limit) break
    from += limit
  }
  return allData
}

const loadAnalyticsData = async () => {
  loading.value = true
  try {
    const { data: targets } = await supabase.from('subject_targets').select('*').order('grade', { ascending: true })
    allTargets.value = targets || []

    const { data: classes } = await supabase.from('classes').select('*')
    allClasses.value = classes || []

    const { data: teachers } = await supabase.from('teachers').select('*')
    allTeachers.value = teachers || []

    const { data: weeks } = await supabase.from('school_weeks').select('*').order('week_number', { ascending: true })
    allSchoolWeeks.value = weeks || []

    const timetables = await fetchAllRows('timetable')
    const interruptions = await fetchAllRows('mmi_interruptions')
    const leaveRequests = await fetchAllRows('leave_requests')

    const teacherMap = {}
    if (teachers) {
      teachers.forEach(tch => {
        if (tch.id) teacherMap[String(tch.id)] = tch
        if (tch.name) teacherMap[cleanString(tch.name)] = tch
      })
    }

    const enrichedTimetables = (timetables || []).map(item => {
      const tIdKey = item.teacher_id ? String(item.teacher_id) : ''
      const tNameKey = item.teacher_name ? cleanString(item.teacher_name) : ''
      const teacherObj = teacherMap[tIdKey] || teacherMap[tNameKey] || {}
      return {
        ...item,
        teacher_info: teacherObj,
        resolved_teacher_name: teacherObj.name || item.teacher_name || item.teacher || ''
      }
    })

    const today = new Date().toISOString().split('T')[0]
    const currentWeek = (weeks || []).filter(w => w.is_school_week && w.end_date <= today).length || 27
    const validWeeksCount = (weeks || []).filter(w => w.is_school_week).length
    const totalSchoolWeeks = validWeeksCount > 0 ? validWeeksCount : 42
    let results = []
    
    for (const cls of (classes || [])) {
      const gradeTargets = (targets || []).filter(t => Number(t.grade) === Number(cls.grade))

      for (const t of gradeTargets) {
        const totalTarget = t.planned_periods || 215 
        const standardizedTargetSubject = standardizeSubjectName(t.subject_name)

        const matchedEntries = enrichedTimetables.filter(item => {
          const itemClass = cleanString(item.class_name)
          const clsName = cleanString(cls.class_name)
          const isClassMatched = itemClass === clsName || itemClass.includes(clsName) || clsName.includes(itemClass)
          if (!isClassMatched) return false

          const rawSubj = item.subject || item.subject_name || item.teacher_info?.subject_name || ''
          return isSubjectMatch(rawSubj, standardizedTargetSubject)
        })

        const assignedTeacherNames = [...new Set(matchedEntries.map(e => e.resolved_teacher_name).filter(Boolean))]
        const assignedTeacherIds = [...new Set(matchedEntries.map(e => e.teacher_id || e.teacher_info?.id).filter(Boolean))]
        const assignedTeacherName = assignedTeacherNames.length > 0 ? assignedTeacherNames.join(' / ') : 'BELUM DITETAPKAN'

        const uniquePeriods = new Set(matchedEntries.map(e => `${e.weekday}-${e.period}`))
        const weeklyPeriods = uniquePeriods.size

        let lostCount = 0

        if (leaveRequests && leaveRequests.length > 0) {
          leaveRequests.forEach(req => {
            const reqClass = cleanString(req.class_name)
            const clsName = cleanString(cls.class_name)
            const isClassMatched = reqClass === clsName || reqClass.includes(clsName) || clsName.includes(reqClass)
            const isSubjMatched = isSubjectMatch(req.subject, standardizedTargetSubject)

            const reqTeacherNameClean = cleanString(req.teacher_name)
            const isTeacherMatched = 
              assignedTeacherIds.some(id => req.teacher_id && String(req.teacher_id) === String(id)) ||
              assignedTeacherNames.some(name => reqTeacherNameClean && cleanString(name) === reqTeacherNameClean)

            if (isTeacherMatched && isClassMatched && isSubjMatched) {
              if (req.leave_date) {
                const leaveDateObj = new Date(req.leave_date)
                const leaveWeekday = leaveDateObj.getDay()
                
                const hasClassOnThatDay = enrichedTimetables.some(item => {
                  const itemClass = cleanString(item.class_name)
                  const itemSubj = item.subject || item.subject_name || item.teacher_info?.subject_name || ''
                  const itemWeekday = Number(item.weekday)
                  
                  const matchCls = itemClass === clsName || itemClass.includes(clsName) || clsName.includes(itemClass)
                  const matchSubj = isSubjectMatch(itemSubj, standardizedTargetSubject)
                  const matchWd = itemWeekday === leaveWeekday || itemWeekday === (leaveWeekday === 0 ? 7 : leaveWeekday)
                  
                  return matchCls && matchSubj && matchWd
                })

                if (hasClassOnThatDay) {
                  lostCount += 1
                }
              } else {
                lostCount += 1
              }
            }
          })
        }

        if (interruptions && interruptions.length > 0) {
          interruptions.forEach(int => {
            if (int.type === 'class') {
              const startP = Number(int.start_period) || 1
              const endP = Number(int.end_period) || 1
              const intScope = int.scope ? int.scope.trim() : ''
              const targetDisp = int.target_display ? int.target_display.trim() : ''
              const intGrade = Number(int.grade)
              const intClass = cleanString(int.class_name)
              const clsName = cleanString(cls.class_name)

              const intDate = new Date(int.interruption_date)
              const intWeekday = intDate.getDay()

              const isClassAffected = 
                intScope === 'all' || 
                targetDisp.includes('SEMUA') || targetDisp.includes('全校') ||
                (intScope === 'grade' && intGrade === Number(cls.grade)) ||
                targetDisp.includes(`TAHUN ${cls.grade}`) || targetDisp.includes(`Tahun ${cls.grade}`) ||
                (intScope === 'class' && (intClass.includes(clsName) || clsName.includes(intClass || ''))) ||
                targetDisp.includes(clsName)

              if (isClassAffected) {
                const affectedPeriods = enrichedTimetables.filter(item => {
                  const itemClass = cleanString(item.class_name)
                  const itemPeriod = Number(item.period)
                  const rawSubj = item.subject || item.subject_name || item.teacher_info?.subject_name || ''
                  const itemWeekday = Number(item.weekday)

                  const matchClass = itemClass === clsName || itemClass.includes(clsName) || clsName.includes(itemClass)
                  const matchPeriod = itemPeriod >= startP && itemPeriod <= endP
                  const matchSubject = isSubjectMatch(rawSubj, standardizedTargetSubject)
                  const matchWeekday = itemWeekday === intWeekday || itemWeekday === (intWeekday === 0 ? 7 : intWeekday)

                  return matchClass && matchPeriod && matchSubject && matchWeekday
                })

                lostCount += affectedPeriods.length
              }
            }
          })
        }

        const plannedAccumulated = currentWeek * weeklyPeriods
        const actual = Math.max(0, Number((plannedAccumulated - lostCount).toFixed(1)))
        const moeTarget = t.kpm_min_hours || 160
        const theoryProgress = Math.round(moeTarget * (currentWeek / totalSchoolWeeks))
        const gap = Number((actual - theoryProgress).toFixed(1))
        
        let status = 'MENCAPAI SASARAN'
        if (gap < 0) {
          status = 'BELUM MENCAPAI SASARAN'
        } else if (gap > 0) {
          status = 'MELEPASI SASARAN'
        } else {
          status = 'MENCAPAI SASARAN'
        }

        results.push({
          grade: cls.grade,
          class_name: cls.class_name,
          subject_name: t.subject_name,
          teacher_name: assignedTeacherName,
          target: totalTarget,
          expected: theoryProgress,
          lostCount: lostCount,
          actual: actual,
          gap: gap,
          status: status
        })
      }
    }

    analysisList.value = results
    toast.success("DATA ANALISIS BERJAYA DISEGAR SEMULA!")
  } catch (err) {
    console.error("RALAT ANALISIS:", err)
    toast.error("GAGAL MEMUATKAN DATA: " + err.message)
  } finally {
    loading.value = false
  }
}

const filteredAnalysisList = computed(() => {
  let list = analysisList.value.filter(item => {
    const matchGrade = filterGrade.value === 'all' || Number(item.grade) === Number(filterGrade.value)
    const matchClass = filterClass.value === 'all' || item.class_name === filterClass.value
    const matchSubject = filterSubject.value === 'all' || standardizeSubjectName(filterSubject.value) === standardizeSubjectName(item.subject_name)
    const matchTeacher = filterTeacher.value === 'all' || (item.teacher_name && cleanString(item.teacher_name) === cleanString(filterTeacher.value))
    return matchGrade && matchClass && matchSubject && matchTeacher
  })

  return list.sort((a, b) => {
    let valA = a[sortKey.value]
    let valB = b[sortKey.value]

    if (valA === undefined || valA === null) valA = ''
    if (valB === undefined || valB === null) valB = ''

    if (typeof valA === 'number' && typeof valB === 'number') {
      return sortOrder.value === 'asc' ? valA - valB : valB - valA
    } else {
      const strA = String(valA)
      const strB = String(valB)
      return sortOrder.value === 'asc' 
        ? strA.localeCompare(strB, 'ms-MY') 
        : strB.localeCompare(strA, 'ms-MY')
    }
  })
})

const analysisSummary = computed(() => {
  const total = filteredAnalysisList.value.length
  const met = filteredAnalysisList.value.filter(i => i.status === 'MENCAPAI SASARAN' || i.status === 'MELEPASI SASARAN').length
  const unmet = filteredAnalysisList.value.filter(i => i.status === 'BELUM MENCAPAI SASARAN').length
  return { total, met, unmet }
})

const completionRate = computed(() => {
  if (analysisSummary.value.total === 0) return 0
  return Math.round((analysisSummary.value.met / analysisSummary.value.total) * 100)
})

const getGradeStats = (g) => {
  const list = filteredAnalysisList.value.filter(i => Number(i.grade) === Number(g))
  const met = list.filter(i => i.status === 'MENCAPAI SASARAN' || i.status === 'MELEPASI SASARAN').length
  const unmet = list.filter(i => i.status === 'BELUM MENCAPAI SASARAN').length
  return { met, unmet, total: list.length }
}

const subjectLossRanking = computed(() => {
  const map = {}
  filteredAnalysisList.value.forEach(i => {
    if (!map[i.subject_name]) map[i.subject_name] = 0
    map[i.subject_name] += i.lostCount
  })
  return Object.keys(map).map(subj => ({
    subject: subj,
    lost: map[subj]
  })).sort((a, b) => b.lost - a.lost)
})

const maxSubjectLoss = computed(() => {
  if (subjectLossRanking.value.length === 0) return 1
  return Math.max(...subjectLossRanking.value.map(i => i.lost), 1)
})

const exportPdfReport = () => {
  window.print()
}

const addTarget = async () => {
  if (!newTarget.value.subject_name.trim()) return toast.error("SILA MASUKKAN NAMA SUBJEK!")
  try {
    const { error } = await supabase.from('subject_targets').insert([{
      grade: newTarget.value.grade,
      subject_name: newTarget.value.subject_name.trim().toUpperCase(),
      planned_periods: newTarget.value.planned_periods,
      kpm_min_hours: newTarget.value.kpm_min_hours
    }])
    if (error) throw error
    toast.success("SASARAN BERJAYA DITAMBAH!")
    newTarget.value.subject_name = ''
    loadAnalyticsData()
  } catch (err) {
    toast.error("GAGAL MENAMBAH: " + err.message)
  }
}

const deleteTarget = async (id) => {
  try {
    const { error } = await supabase.from('subject_targets').delete().eq('id', id)
    if (error) throw error
    toast.success("SASARAN TELAH DIPADAM")
    loadAnalyticsData()
  } catch (err) {
    toast.error("GAGAL MEMADAM: " + err.message)
  }
}

onMounted(() => {
  loadAnalyticsData()
})

onActivated(() => {
  loadAnalyticsData()
})
</script>

<style scoped>
.print-header {
  display: none;
}

@media print {
  @page {
    size: portrait;
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
  #pdfContentContainer {
    box-shadow: none !important;
    padding: 0 !important;
  }
  .print-summary {
    display: grid !important;
    grid-template-columns: repeat(3, 1fr) !important;
    gap: 15px !important;
    margin-bottom: 20px;
  }
  .print-summary > div {
    border: 1px solid #94a3b8 !important;
    padding: 15px !important;
    border-radius: 12px !important;
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