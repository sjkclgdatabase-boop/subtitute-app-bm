<template>
  <aside class="w-64 bg-slate-900 text-slate-300 flex flex-col h-screen sticky top-0 left-0 shrink-0 border-r border-slate-800 print:hidden shadow-xl">
    
    <!-- 顶部：Logo 与学校名称 -->
    <div class="p-5 border-b border-slate-800 flex items-center gap-3 cursor-pointer select-none" @click="router.push('/')">
      <img 
        :src="currentLogo" 
        alt="Logo" 
        class="w-10 h-10 object-contain rounded-xl bg-slate-800 p-1 shadow-sm shrink-0"
      />
      <div class="flex flex-col justify-center overflow-hidden">
        <span class="font-extrabold text-xs tracking-tight text-white leading-tight truncate">
          {{ currentSchoolName }}
        </span>
        <span class="text-[9px] font-bold text-indigo-400 tracking-wider mt-0.5 uppercase">
          SISTEM PENGURUSAN PINTAR
        </span>
      </div>
    </div>

    <!-- 中部：垂直滚动导航菜单 -->
    <div class="flex-1 overflow-y-auto px-3 py-4 space-y-1.5 scrollbar-thin scrollbar-thumb-slate-700">
      <router-link 
        v-for="(item, index) in navItems" 
        :key="index"
        :to="item.path" 
        class="flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-xs font-bold transition-all duration-200 text-slate-400 hover:text-white hover:bg-slate-800/80 group"
        exact-active-class="!text-white bg-indigo-600 shadow-md shadow-indigo-600/20"
      >
        <span class="text-base shrink-0 group-hover:scale-110 transition-transform">{{ item.icon }}</span>
        <span class="truncate">{{ item.name }}</span>
      </router-link>
    </div>

    <!-- 底部操作区 -->
    <div class="p-4 border-t border-slate-800 bg-slate-950/40 space-y-2">
      
      <!-- 🚀 新增：切换回中文版按钮 -->
      <button 
        @click="switchToChinese" 
        class="w-full flex items-center gap-2 px-3.5 py-2.5 rounded-xl text-xs font-bold text-indigo-400 hover:bg-indigo-500/10 hover:text-indigo-300 transition-colors cursor-pointer"
      >
        <span>🌐</span>
        <span>中文版</span>
      </button>

      <!-- 退出登录按钮 -->
      <button 
        @click="logout" 
        class="w-full flex items-center justify-between px-3.5 py-2.5 rounded-xl text-xs font-bold text-rose-400 hover:bg-rose-500/10 hover:text-rose-300 transition-colors cursor-pointer"
      >
        <div class="flex items-center gap-2">
          <span>🚪</span>
          <span>LOG KELUAR</span>
        </div>
        <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" />
        </svg>
      </button>
    </div>

  </aside>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { supabase } from '../services/supabase'
import { useToast } from '../utils/toast'

const router = useRouter()
const toast = useToast()

const currentLogo = ref('/logo.png')
const currentSchoolName = ref('SISTEM PENGURUSAN AKADEMIK PINTAR')

const loadSidebarIdentity = async () => {
  const cachedLogo = localStorage.getItem('school_logo')
  const cachedName = localStorage.getItem('school_name')
  
  if (cachedLogo && cachedLogo.trim() !== '') currentLogo.value = cachedLogo
  if (cachedName && cachedName.trim() !== '') currentSchoolName.value = cachedName

  try {
    const { data } = await supabase.from('school_settings').select('*').eq('id', 1).single()
    if (data) {
      if (data.logo_url && data.logo_url.trim() !== '') {
        currentLogo.value = data.logo_url
        localStorage.setItem('school_logo', data.logo_url)
      }
      if (data.school_name && data.school_name.trim() !== '') {
        currentSchoolName.value = data.school_name
        localStorage.setItem('school_name', data.school_name)
      }
    }
  } catch (err) {
    console.error('GAGAL MEMUATKAN TETAPAN SIDEBAR:', err)
  }
}

onMounted(() => {
  loadSidebarIdentity()
})

const navItems = [
  { name: 'GAMBARAN KESELURUHAN', path: '/', icon: '📊' },
  { name: 'PENDAFTARAN CUTI GURU', path: '/leave-entry', icon: '📝' },
  { name: 'PROFIL GURU', path: '/teachers', icon: '👩‍🏫' },
  { name: 'JADUAL WAKTU', path: '/timetable', icon: '📅' },
  { name: 'GURU GANTI', path: '/records', icon: '🔄' },
  { name: 'PUSAT LAPORAN MMI', path: '/statistics', icon: '📈' },
  { name: 'REKOD GANGGUAN MMI', path: '/mmi-interruption', icon: '⚠️' }, 
  { name: 'PUSAT ANALISIS', path: '/subject-analysis', icon: '🎯' },
  { name: 'TETAPAN SISTEM', path: '/settings', icon: '⚙️' } 
]

// 🚀 新增：切回中文版逻辑（带上 Token）
const switchToChinese = async () => {
  try {
    const { data: { session } } = await supabase.auth.getSession()
    
    // ⚠️ 记得把这个网址换成你中文版部署后的真实网址
    const chineseAppUrl = 'https://subtitute-app.vercel.app/login' 

    if (session) {
      window.location.href = `${chineseAppUrl}/?access_token=${session.access_token}&refresh_token=${session.refresh_token}`
    } else {
      window.location.href = chineseAppUrl
    }
  } catch (error) {
    toast.error("Gagal bertukar: " + error.message)
  }
}

const logout = async () => {
  try {
    await supabase.auth.signOut()
    toast.success("BERJAYA LOG KELUAR")
    
    // 🚀 核心修改：用原生强制跳转替换 router.push，彻底清空残留界面
    window.location.href = '/login'
    
  } catch (error) {
    toast.error("LOG KELUAR GAGAL: " + error.message)
  }
}
</script>