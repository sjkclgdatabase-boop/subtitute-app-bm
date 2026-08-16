<template>
  <div class="min-h-screen bg-slate-50 flex">
    <!-- 左侧边栏：非登录页时显示，且打印时自动隐藏 -->
    <Sidebar v-if="showSidebar" />
    
    <!-- 右侧主体内容区 -->
    <main class="flex-1 min-w-0 overflow-y-auto">
      <router-view />
    </main>
    
    <Toast />
  </div>
</template>

<script setup>
import { computed, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { supabase } from './services/supabase'
import Sidebar from './components/Sidebar.vue'
import Toast from './components/Toast.vue'

const route = useRoute()
const router = useRouter()
const showSidebar = computed(() => route.path !== '/login')

// 🚀 核心：页面加载时拦截 URL 中的 Token，自动注入登录状态
onMounted(async () => {
  const urlParams = new URLSearchParams(window.location.search)
  const accessToken = urlParams.get('access_token')
  const refreshToken = urlParams.get('refresh_token')

  if (accessToken && refreshToken) {
    try {
      // 使用 token 恢复 Supabase 会话
      const { error } = await supabase.auth.setSession({
        access_token: accessToken,
        refresh_token: refreshToken
      })

      if (!error) {
        // 成功登录后，清理掉网址上的 token 参数，保持地址栏干净
        window.history.replaceState({}, document.title, window.location.pathname)
      }
    } catch (err) {
      console.error('Gagal memulihkan sesi:', err)
    }
  }
})
</script>