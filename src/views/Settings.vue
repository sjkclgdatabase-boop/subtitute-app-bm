<template>
  <div class="p-8 max-w-7xl mx-auto min-h-screen space-y-8">
    
    <!-- 头部区域：统一的卡片风格、排版规范与渐变大标题 -->
    <div class="bg-white rounded-3xl p-8 shadow-sm ring-1 ring-slate-900/5 space-y-2">
      <h1 class="text-2xl sm:text-3xl font-extrabold tracking-tight text-transparent bg-clip-text bg-gradient-to-r from-slate-900 via-indigo-800 to-violet-800">
        TETAPAN & PENYENGGARAN SISTEM KESELURUHAN
      </h1>
      <p class="text-slate-500 text-xs sm:text-sm font-medium leading-relaxed">
        KONFIGURASI PARAMETER WAKTU SEKOLAH, PENGURUSAN KELAS ASAS, TETAPAN SASARAN MMI, SOKONG PEMBERSIHAN & PEMULIHAN DATA SEBELUM PELANCARAN.
      </p>
    </div>

    <!-- 卡片：学校 Logo 与外观设置 -->
    <div class="bg-white rounded-3xl shadow-sm ring-1 ring-slate-900/5 p-8 transition-all duration-300 hover:shadow-md space-y-6">
      <h2 class="text-lg font-bold text-slate-900 mb-6 flex items-center gap-2">
        <span class="w-8 h-8 rounded-2xl bg-cyan-50 text-cyan-600 flex items-center justify-center font-bold text-xs">🖼️</span>
        TETAPAN LOGO & PAPARAN SEKOLAH
      </h2>
      <p class="text-slate-500 text-xs font-medium">KEMASKINI LOGO & NAMA SEKOLAH. SELEPAS DISIMPAN, BAR MENU DAN LAMAN LOG MASUK AKAN DIKEMASKINI SECARA MASA NYATA.</p>

      <div class="space-y-6">
        <div>
          <label class="block text-xs font-bold text-slate-700 mb-2 uppercase tracking-wider">NAMA SEKOLAH SEMASA</label>
          <input 
            v-model="schoolNameSetting" 
            type="text" 
            placeholder="MASUKKAN NAMA SEKOLAH..." 
            class="w-full sm:w-96 px-4 h-11 bg-slate-50 border border-slate-200 rounded-2xl text-xs font-bold text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500/50 transition-all"
          />
        </div>

        <div>
          <label class="block text-xs font-bold text-slate-700 mb-2 uppercase tracking-wider">TUKAR LOGO SEKOLAH (SOKONG MUAT NAIK TEMPATAN)</label>
          
          <div class="flex items-center gap-6">
            <!-- 预览图 -->
            <div class="w-20 h-20 rounded-2xl border border-slate-200 bg-slate-50 flex items-center justify-center p-2 overflow-hidden shadow-inner shrink-0">
              <img :src="schoolLogoSetting || '/logo.png'" alt="Logo Preview" class="w-full h-full object-contain" />
            </div>

            <!-- 上传按钮与说明 -->
            <div class="flex-1 space-y-2">
              <label class="relative inline-flex cursor-pointer bg-slate-900 hover:bg-slate-800 text-white px-5 h-11 rounded-2xl text-xs font-bold transition shadow-sm items-center gap-2">
                <span>📂 PILIH IMEJ LOGO BAHARU</span>
                <input type="file" accept="image/*" @change="handleSettingsLogoUpload" class="absolute inset-0 opacity-0 cursor-pointer w-full h-full" />
              </label>
              <p class="text-[11px] text-slate-400 font-medium">SOKONG FORMAT PNG, JPG, SAIZ DI BAWAH 2MB ADALAH DIGALAKKAN.</p>
            </div>
          </div>
        </div>
      </div>

      <div class="mt-10 pt-6 border-t border-slate-100 flex justify-end">
        <button 
          @click="saveSchoolIdentity" 
          class="bg-indigo-600 hover:bg-indigo-700 text-white px-6 h-11 rounded-2xl text-xs font-bold shadow-sm transition-all duration-200 cursor-pointer"
        >
          SIMPAN PERUBAHAN LOGO & NAMA
        </button>
      </div>
    </div>

    <!-- 卡片一：学校作息配置 -->
    <div class="bg-white rounded-3xl shadow-sm ring-1 ring-slate-900/5 p-8 transition-all duration-300 hover:shadow-md space-y-6">
      <h2 class="text-lg font-bold text-slate-900 mb-6 flex items-center gap-2">
        <span class="w-8 h-8 rounded-2xl bg-indigo-50 text-indigo-600 flex items-center justify-center font-bold text-xs">🏫</span>
        KONFIGURASI WAKTU PERSEKOLAHAN
      </h2>
      
      <div class="space-y-6">
        <div>
          <label class="block text-xs font-bold text-slate-700 mb-2 uppercase tracking-wider">BILANGAN HARI PERSEKOLAHAN SEMINGGU (HARI)</label>
          <div class="flex items-center gap-4">
            <input 
              v-model.number="config.daysPerWeek" 
              type="number" min="1" max="7" 
              class="w-full sm:w-64 px-4 h-11 bg-slate-50 border border-slate-200 rounded-2xl text-xs font-bold text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500/50 transition-all"
            />
            <span class="text-xs text-slate-400 font-medium">CONTOH: 5 (MEWAKILI ISNIN HINGGA JUMAAT)</span>
          </div>
        </div>

        <div>
          <label class="block text-xs font-bold text-slate-700 mb-2 uppercase tracking-wider">JUMLAH SLOT MASA SEHARI (SLOT)</label>
          <div class="flex items-center gap-4">
            <input 
              v-model.number="config.periodsPerDay" 
              type="number" min="1" max="15" 
              class="w-full sm:w-64 px-4 h-11 bg-slate-50 border border-slate-200 rounded-2xl text-xs font-bold text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500/50 transition-all"
            />
            <span class="text-xs text-slate-400 font-medium">CONTOH: 11 (JUMLAH SLOT SESI PAGI)</span>
          </div>
        </div>
      </div>

      <div class="mt-10 pt-6 border-t border-slate-100 flex justify-end">
        <button 
          @click="saveConfig" 
          class="bg-slate-900 hover:bg-slate-800 text-white px-6 h-11 rounded-2xl text-xs font-bold shadow-sm transition-all duration-200 cursor-pointer"
        >
          SIMPAN KONFIGURASI
        </button>
      </div>
    </div>

    <!-- 卡片二：学年上课周历与假期维护 -->
    <div class="bg-white rounded-3xl shadow-sm ring-1 ring-slate-900/5 p-8 transition-all duration-300 hover:shadow-md space-y-6">
      <h2 class="text-lg font-bold text-slate-900 mb-2 flex items-center gap-2">
        <span class="w-8 h-8 rounded-2xl bg-indigo-50 text-indigo-700 flex items-center justify-center font-bold text-xs">📅</span>
        KONFIGURASI KALENDAR & CUTI PERSEKOLAHAN
      </h2>
      <p class="text-slate-500 text-xs font-medium mb-6">Masukkan minggu persekolahan dan cuti sepanjang tahun. Sistem akan menolak tempoh cuti secara automatik untuk mengira sasaran tahunan dengan tepat.</p>

      <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 p-5 bg-slate-50 rounded-2xl border border-slate-200 mb-6">
        <div>
          <h3 class="text-xs font-bold uppercase tracking-wider text-slate-950">IMPORT / EKSPORT KALENDAR PERSEKOLAHAN BERKELOMPOK</h3>
          <p class="text-slate-500 text-xs mt-1 font-medium">SOKONG MUAT TURUN TEMPLAT CSV PIAWAI, ISI DAN MUAT NAIK BERKELOMPOK.</p>
        </div>
        <div class="flex flex-wrap items-center gap-3">
          <button @click="downloadWeekTemplate" class="bg-white hover:bg-slate-100 text-slate-700 border border-slate-200 px-4 h-11 rounded-2xl text-xs font-bold transition shadow-sm flex items-center gap-2 cursor-pointer">
            📥 MUAT TURUN TEMPLAT KALENDAR
          </button>
          
          <label class="relative cursor-pointer bg-indigo-600 hover:bg-indigo-700 text-white px-4 h-11 rounded-2xl text-xs font-bold transition shadow-sm flex items-center gap-2">
            <span>📂 PILIH FAIL KALENDAR</span>
            <input type="file" ref="weekFileInput" accept=".csv" @change="handleWeekFileUpload" class="absolute inset-0 opacity-0 cursor-pointer w-full h-full" />
          </label>
        </div>
      </div>

      <div class="bg-slate-50 p-5 rounded-2xl border border-slate-200 mb-6 grid grid-cols-1 sm:grid-cols-5 gap-3 items-end">
        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">MINGGU KE-</label>
          <input type="number" v-model.number="newWeek.week_number" placeholder="1" class="w-full bg-white border border-slate-200 px-3 h-11 rounded-xl text-xs font-bold text-slate-800" />
        </div>
        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">TARIKH MULA (YYYY-MM-DD)</label>
          <input type="date" v-model="newWeek.start_date" class="w-full bg-white border border-slate-200 px-3 h-11 rounded-xl text-xs font-bold text-slate-800 cursor-pointer" />
        </div>
        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">TARIKH TAMAT (YYYY-MM-DD)</label>
          <input type="date" v-model="newWeek.end_date" class="w-full bg-white border border-slate-200 px-3 h-11 rounded-xl text-xs font-bold text-slate-800 cursor-pointer" />
        </div>
        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">JENIS</label>
          <select v-model="newWeek.is_school_week" class="w-full bg-white border border-slate-200 px-3 h-11 rounded-xl text-xs font-bold text-slate-800 cursor-pointer">
            <option :value="true">🟢 MINGGU PERSEKOLAHAN</option>
            <option :value="false">🔴 MINGGU CUTI</option>
          </select>
        </div>
        <button @click="addSchoolWeek" class="bg-slate-900 hover:bg-slate-800 text-white h-11 rounded-xl text-xs font-bold shadow-md transition-all cursor-pointer">
          ➕ TAMBAH SATU
        </button>
      </div>

      <div class="space-y-3">
        <div class="flex items-center justify-between mb-2">
          <h3 class="text-xs font-bold uppercase tracking-wider text-slate-500">SENARAI KALENDAR PERSEKOLAHAN YANG TELAH DISIMPAN</h3>
          <span class="text-xs bg-indigo-50 text-indigo-700 px-2.5 py-1 rounded-full font-bold">JUMLAH MINGGU {{ allSchoolWeeks.length }}</span>
        </div>

        <div class="divide-y divide-slate-100 border border-slate-200 rounded-2xl overflow-hidden max-h-64 overflow-y-auto">
          <div v-if="allSchoolWeeks.length === 0" class="p-8 text-center text-xs text-slate-400 font-medium">
            TIADA DATA KALENDAR DIMASUKKAN, SILA TAMBAH ATAU IMPORT DI ATAS.
          </div>
          <div v-for="w in allSchoolWeeks" :key="w.id" class="p-3.5 flex items-center justify-between bg-white hover:bg-slate-50 transition">
            <div class="flex items-center gap-4 text-xs font-bold text-slate-700">
              <span class="px-2.5 py-1 bg-indigo-50 text-indigo-700 rounded-xl font-bold">MINGGU KE-{{ w.week_number }}</span>
              <span class="text-slate-600 font-medium">{{ w.start_date }} ~ {{ w.end_date }}</span>
              <span :class="w.is_school_week ? 'text-emerald-700 bg-emerald-50 border-emerald-200' : 'text-red-700 bg-red-50 border-red-200'" class="px-2.5 py-1 rounded-full text-[10px] font-black border">
                {{ w.is_school_week ? '🟢 MINGGU PERSEKOLAHAN' : '🔴 MINGGU CUTI' }}
              </span>
            </div>
            <button @click="deleteSchoolWeek(w.id)" class="text-slate-400 hover:text-red-600 text-xs font-bold px-3 py-1.5 rounded-xl transition hover:bg-red-50 cursor-pointer">
              🗑️ PADAM
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- 卡片三：学校班级基础管理 -->
    <div class="bg-white rounded-3xl shadow-sm ring-1 ring-slate-900/5 p-8 transition-all duration-300 hover:shadow-md space-y-6">
      <h2 class="text-lg font-bold text-slate-900 mb-6 flex items-center gap-2">
        <span class="w-8 h-8 rounded-2xl bg-violet-50 text-violet-600 flex items-center justify-center font-bold text-xs">📚</span>
        PENGURUSAN KELAS ASAS SEKOLAH
      </h2>
      <p class="text-slate-500 text-xs font-medium mb-6">SELENGGARA KELAS PIAWAI SEKOLAH, UNTUK KEGUNAAN JADUAL, CUTI & REKOD GANGGUAN MMI.</p>

      <div class="bg-slate-50 p-5 rounded-2xl border border-slate-200 mb-8 flex flex-col sm:flex-row gap-4 items-end">
        <div class="w-full sm:w-1/3">
          <label class="block text-xs font-bold text-slate-700 mb-2 uppercase tracking-wider">TAHUN:</label>
          <select v-model="newClassGrade" class="w-full bg-white border border-slate-200 px-4 h-11 rounded-2xl text-xs font-bold text-slate-800 cursor-pointer">
            <option v-for="g in [1, 2, 3, 4, 5, 6]" :key="g" :value="g">TAHUN {{ g }}</option>
          </select>
        </div>

        <div class="w-full sm:w-1/2">
          <label class="block text-xs font-bold text-slate-700 mb-2 uppercase tracking-wider">NAMA KELAS (CONTOH: 1A, 4C):</label>
          <input 
            type="text" 
            v-model="newClassName" 
            placeholder="MASUKKAN NAMA KELAS PIAWAI..." 
            class="w-full bg-white border border-slate-200 px-4 h-11 rounded-2xl text-xs font-bold text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500"
          />
        </div>

        <button 
          @click="addClass" 
          class="w-full sm:w-auto bg-indigo-600 hover:bg-indigo-700 text-white px-6 h-11 rounded-2xl text-xs font-bold shadow-md transition-all shrink-0 cursor-pointer"
        >
          ➕ TAMBAH KELAS
        </button>
      </div>

      <div class="space-y-6">
        <div class="flex items-center justify-between">
          <h3 class="text-xs font-bold uppercase tracking-wider text-slate-500">SENARAI KELAS SEKOLAH DIMASUKKAN</h3>
          <button @click="toggleAllGrades" class="text-xs text-indigo-600 font-bold hover:underline cursor-pointer">
            {{ allExpanded ? 'LIPAT SEMUA' : 'BUKA SEMUA' }}
          </button>
        </div>
        
        <div v-if="classList.length === 0" class="text-xs text-slate-400 font-medium py-6 text-center border border-dashed border-slate-200 rounded-2xl">
          TIADA DATA KELAS, SILA TAMBAH DI ATAS
        </div>

        <div v-else class="space-y-4">
          <div v-for="(classListGroup, gradeKey) in groupedClasses" :key="gradeKey" class="space-y-2">
            
            <div v-if="classListGroup.length > 0" 
                 @click="toggleGrade(gradeKey)"
                 class="flex items-center gap-3 cursor-pointer group select-none">
              <span class="px-3 py-1 bg-violet-50 text-violet-700 rounded-xl text-xs font-black tracking-wider group-hover:bg-violet-100 transition flex items-center gap-2">
                TAHUN {{ gradeKey }} 
                <span class="text-[10px] text-violet-400">{{ expandedGrades[gradeKey] ? '▼' : '▶' }}</span>
              </span>
              <div class="h-px bg-slate-100 flex-1"></div>
            </div>

            <div v-show="expandedGrades[gradeKey]" class="flex flex-wrap gap-3 pt-1">
              <div 
                v-for="c in classListGroup" 
                :key="c.id"
                class="p-3 bg-white border border-slate-200 rounded-2xl shadow-sm flex items-center justify-between group hover:border-indigo-300 transition-all min-w-[120px]"
              >
                <div>
                  <div class="text-xs font-bold text-slate-900">{{ c.class_name }}</div>
                  <div class="text-[10px] text-slate-400 font-medium">TAHUN {{ c.grade }}</div>
                </div>
                <button 
                  @click.stop="deleteClass(c.id)" 
                  class="text-slate-300 hover:text-red-600 text-xs font-bold p-1 transition opacity-0 group-hover:opacity-100 ml-3 cursor-pointer"
                  title="PADAM"
                >
                  ✕
                </button>
              </div>
            </div>

          </div>
        </div>
      </div>
    </div>

    <!-- 卡片四：MMI 科目目标模板管理与批量导入 -->
    <div class="bg-white rounded-3xl shadow-sm ring-1 ring-slate-900/5 p-8 transition-all duration-300 hover:shadow-md space-y-6">
      <h2 class="text-lg font-bold text-slate-900 mb-6 flex items-center gap-2">
        <span class="w-8 h-8 rounded-2xl bg-emerald-50 text-emerald-600 flex items-center justify-center font-bold text-xs">📊</span>
        KONFIGURASI BERKELOMPOK SASARAN SUBJEK MMI
      </h2>
      <p class="text-slate-500 text-xs font-medium mb-6">MUAT TURUN TEMPLAT PIAWAI, ISI SASARAN SLOT DAN ASAS UNTUK SETIAP TAHUN DAN SUBJEK, IMPORT BERKELOMPOK.</p>

      <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 p-5 bg-slate-50 rounded-2xl border border-slate-200">
        <div>
          <h3 class="text-xs font-bold uppercase tracking-wider text-slate-950">IMPORT / EKSPORT SASARAN SUBJEK</h3>
          <p class="text-slate-500 text-xs mt-1 font-medium">SOKONG TEMPLAT CSV, IMPORT AUTOMATIK KEMASKINI KONFIGURASI SASARAN PANGKALAN DATA.</p>
        </div>
        <div class="flex flex-wrap items-center gap-3">
          <button @click="downloadTemplate" class="bg-white hover:bg-slate-100 text-slate-700 border border-slate-200 px-4 h-11 rounded-2xl text-xs font-bold transition shadow-sm flex items-center gap-2 cursor-pointer">
            📥 MUAT TURUN TEMPLAT
          </button>
          
          <label class="relative cursor-pointer bg-indigo-600 hover:bg-indigo-700 text-white px-4 h-11 rounded-2xl text-xs font-bold transition shadow-sm flex items-center gap-2">
            <span>📂 PILIH FAIL SUBJEK</span>
            <input type="file" ref="fileInput" accept=".csv" @change="handleFileUpload" class="absolute inset-0 opacity-0 cursor-pointer w-full h-full" />
          </label>
        </div>
      </div>
    </div>

    <!-- 卡片五：本地数据备份与恢复 -->
    <div class="bg-white rounded-3xl shadow-sm ring-1 ring-slate-900/5 p-8 transition-all duration-300 hover:shadow-md space-y-6">
      <h2 class="text-lg font-bold text-slate-900 mb-6 flex items-center gap-2">
        <span class="w-8 h-8 rounded-2xl bg-blue-50 text-blue-600 flex items-center justify-center font-bold text-xs">💾</span>
        SANDARAN & PEMULIHAN DATA TEMPATAN
      </h2>
      <p class="text-slate-500 text-xs font-medium mb-6">Buat sandaran berkala bagi semua data teras sekolah dan simpan dalam komputer tempatan untuk memastikan data sentiasa selamat.</p>
      
      <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
        <!-- 导出备份 -->
        <div class="p-5 bg-slate-50 rounded-2xl border border-slate-200 flex flex-col justify-between space-y-4">
          <div>
            <h3 class="text-xs font-bold uppercase tracking-wider text-slate-950">EKSPORT SANDARAN DATA PENUH</h3>
            <p class="text-slate-500 text-xs mt-1 font-medium">MUAT TURUN FAIL SANDARAN JSON YANG MENGANDUNGI DATA UTAMA SEKOLAH.</p>
          </div>
          <button @click="exportFullBackup" class="w-full bg-slate-900 hover:bg-slate-800 text-white h-11 rounded-2xl text-xs font-bold transition shadow-sm cursor-pointer">
            📤 EKSPORT SANDARAN PENUH SISTEM
          </button>
        </div>

        <!-- 恢复备份 -->
        <div class="p-5 bg-slate-50 rounded-2xl border border-slate-200 flex flex-col justify-between space-y-4">
          <div>
            <h3 class="text-xs font-bold uppercase tracking-wider text-slate-950">PULIH DATA SISTEM</h3>
            <p class="text-slate-500 text-xs mt-1 font-medium">PULIH DATA SEKOLAH MELALUI FAIL SANDARAN JSON TERDAHULU.</p>
          </div>
          <label class="relative block w-full text-center cursor-pointer bg-indigo-600 hover:bg-indigo-700 text-white h-11 rounded-2xl text-xs font-bold transition shadow-sm flex items-center justify-center">
            <span>📥 PILIH FAIL SANDARAN & PULIH</span>
            <input type="file" accept=".json" @change="importFullBackup" class="absolute inset-0 opacity-0 cursor-pointer w-full h-full" />
          </label>
        </div>
      </div>
    </div>

    <!-- 卡片六：正式上线数据清理、备份与维护面板 -->
    <div class="bg-white rounded-3xl shadow-sm ring-1 ring-slate-900/5 p-8 transition-all duration-300 hover:shadow-md space-y-6">
      <h2 class="text-lg font-bold text-slate-900 mb-6 flex items-center gap-2">
        <span class="w-8 h-8 rounded-2xl bg-red-50 text-red-600 flex items-center justify-center font-bold text-xs">🛠️</span>
        PEMBERSIHAN & PENYENGGARAN DATA PELANCARAN RASMI
      </h2>
      <p class="text-slate-500 text-xs font-medium mb-6">UNTUK MEMBERSIHKAN DATA UJIAN SEBELUM PELANCARAN RASMI, SOKONG SANDARAN SATU KLIK.</p>
      
      <div class="space-y-6">
        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center pb-6 border-b border-slate-100 gap-4">
          <div>
            <h3 class="text-xs font-bold uppercase tracking-wider text-slate-950">KOSONGKAN SEMUA REKOD CUTI & GURU GANTI</h3>
            <p class="text-slate-500 text-xs mt-1 font-medium">PADAM HANYA DATA UJIAN CUTI & GURU GANTI, KEKALKAN PROFIL GURU & JADUAL.</p>
          </div>
          <!-- ⭐️ 统一加上 w-full sm:w-80 和 justify-center -->
          <button @click="clearOnlyRecords" :disabled="loading" class="w-full sm:w-80 h-11 bg-amber-50 hover:bg-amber-100 text-amber-700 font-bold text-xs rounded-2xl transition shadow-sm border border-amber-200 disabled:opacity-50 shrink-0 cursor-pointer flex items-center justify-center">
            KOSONGKAN REKOD CUTI
          </button>
        </div>

        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center pb-6 border-b border-slate-100 gap-4">
          <div>
            <h3 class="text-xs font-bold uppercase tracking-wider text-slate-950">TETAP SEMULA JADUAL WAKTU SEKOLAH</h3>
            <p class="text-slate-500 text-xs mt-1 font-medium">KOSONGKAN SEMUA DATA JADUAL WAKTU, MEMUDAHKAN IMPORT JADUAL BAHARU.</p>
          </div>
          <!-- ⭐️ 统一加上 w-full sm:w-80 和 justify-center -->
          <button @click="clearOnlyTimetable" :disabled="loading" class="w-full sm:w-80 h-11 bg-orange-50 hover:bg-orange-100 text-orange-700 font-bold text-xs rounded-2xl transition shadow-sm border border-orange-200 disabled:opacity-50 shrink-0 cursor-pointer flex items-center justify-center">
            KOSONGKAN JADUAL WAKTU SEKOLAH
          </button>
        </div>

        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center pb-6 border-b border-slate-100 gap-4">
          <div>
            <h3 class="text-xs font-bold uppercase tracking-wider text-indigo-900">SANDARAN & KOSONGKAN DATA SEJARAH GANGGUAN MMI</h3>
            <p class="text-slate-500 text-xs mt-1 font-medium">MUAT TURUN SANDARAN JSON, KEMUDIAN PADAM SEMUA LOG GANGGUAN MMI DENGAN SELAMAT.</p>
          </div>
          <!-- ⭐️ 统一加上 w-full sm:w-80 和 justify-center -->
          <button @click="backupAndClearMmi" :disabled="loading" class="w-full sm:w-80 h-11 bg-indigo-50 hover:bg-indigo-100 text-indigo-700 font-bold text-xs rounded-2xl transition shadow-sm border border-indigo-200 disabled:opacity-50 shrink-0 cursor-pointer flex items-center justify-center">
            📥 SANDARAN & KOSONGKAN DATA MMI
          </button>
        </div>

        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
          <div>
            <h3 class="text-xs font-bold uppercase tracking-wider text-red-600">OPERASI BERBAHAYA: INISIALISASI SISTEM</h3>
            <p class="text-slate-500 text-xs mt-1 font-medium">KOSONGKAN CUTI, GURU GANTI, JADUAL, SENARAI GURU, PULIH KEPADA KEADAAN ASAL.</p>
          </div>
          <!-- ⭐️ 统一加上 w-full sm:w-80 和 justify-center -->
          <button @click="clearEverything" :disabled="loading" class="w-full sm:w-80 h-11 bg-red-600 hover:bg-red-700 text-white font-bold text-xs rounded-2xl transition shadow-md disabled:opacity-50 shrink-0 cursor-pointer flex items-center justify-center">
            TETAP SEMULA SISTEM SEPENUHNYA
          </button>
        </div>
      </div>
    </div>

    <!-- 📊 动态数字百分比进度条弹窗 -->
    <Transition
      enter-active-class="transition duration-300 ease-out"
      enter-from-class="opacity-0 scale-95"
      enter-to-class="opacity-100 scale-100"
      leave-active-class="transition duration-200 ease-in"
      leave-from-class="opacity-100 scale-100"
      leave-to-class="opacity-0 scale-95"
    >
      <div v-if="uploadProgress.show" class="fixed inset-0 z-50 flex items-center justify-center bg-slate-900/60 backdrop-blur-sm p-4 select-none">
        <div class="bg-white rounded-3xl max-w-md w-full p-8 shadow-2xl border border-slate-100 text-center space-y-6">
          
          <div class="w-16 h-16 rounded-2xl mx-auto flex items-center justify-center text-3xl transition-all duration-300"
               :class="uploadProgress.percent === 100 ? 'bg-emerald-100 text-emerald-600' : 'bg-indigo-50 text-indigo-600 animate-bounce'">
            <span v-if="uploadProgress.percent < 100">📂</span>
            <span v-else>🎉</span>
          </div>

          <div>
            <h3 class="text-lg font-extrabold text-slate-900">
              {{ uploadProgress.percent === 100 ? 'IMPORT TEMPLAT BERJAYA!' : 'SEDANG MENGIMPORT DATA TEMPLAT...' }}
            </h3>
            <p class="text-xs font-semibold text-slate-500 mt-1.5">
              {{ uploadProgress.statusText }}
            </p>
          </div>

          <div class="space-y-2">
            <div class="w-full h-3.5 bg-slate-100 rounded-full overflow-hidden p-0.5 border border-slate-200/80 shadow-inner">
              <div 
                class="h-full bg-gradient-to-r from-indigo-500 via-purple-500 to-indigo-600 rounded-full transition-all duration-300 shadow-sm"
                :style="{ width: uploadProgress.percent + '%' }"
              ></div>
            </div>
            
            <div class="flex justify-between items-center text-xs font-bold px-1">
              <span class="text-slate-400">KEMAJUAN PEMPROSESAN</span>
              <span class="text-indigo-600 font-black text-sm">{{ uploadProgress.percent }}%</span>
            </div>
          </div>

        </div>
      </div>
    </Transition>

  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { supabase } from '../services/supabase'
import { useToast } from '../utils/toast'

const toast = useToast()
const config = ref({ daysPerWeek: 5, periodsPerDay: 8 })
const loading = ref(false)
const fileInput = ref(null)
const weekFileInput = ref(null)

// 📊 上传百分比进度条状态
const uploadProgress = ref({
  show: false,
  percent: 0,
  statusText: ''
})

const sleep = (ms) => new Promise(resolve => setTimeout(resolve, ms))

const startProgress = (initialText = 'SEDANG MEMBACA FAIL TEMPLAT...') => {
  uploadProgress.value = {
    show: true,
    percent: 10,
    statusText: initialText
  }
}

const updateProgress = (percent, text) => {
  uploadProgress.value.percent = percent
  if (text) uploadProgress.value.statusText = text
}

const finishProgress = async (successMsg = 'IMPORT BERJAYA') => {
  uploadProgress.value.percent = 100
  uploadProgress.value.statusText = successMsg
  await sleep(600)
  uploadProgress.value.show = false
}

// 班级管理状态
const classList = ref([])
const newClassGrade = ref(1)
const newClassName = ref('')

const expandedGrades = ref({ 1: true, 2: true, 3: true, 4: true, 5: true, 6: true })
const allExpanded = computed(() => Object.values(expandedGrades.value).every(v => v))

// 上课周历状态
const allSchoolWeeks = ref([])
const newWeek = ref({ week_number: 1, start_date: '', end_date: '', is_school_week: true })

const toggleGrade = (grade) => {
  expandedGrades.value[grade] = !expandedGrades.value[grade]
}

const toggleAllGrades = () => {
  const target = !allExpanded.value
  Object.keys(expandedGrades.value).forEach(k => {
    expandedGrades.value[k] = target
  })
}

const groupedClasses = computed(() => {
  const groups = { 1: [], 2: [], 3: [], 4: [], 5: [], 6: [] }
  classList.value.forEach(c => {
    const g = c.grade || Number(c.class_name[0]) || 1
    if (groups[g]) {
      groups[g].push(c)
    } else {
      if (!groups['Other']) groups['Other'] = []
      groups['Other'].push(c)
    }
  })
  return groups
})

onMounted(() => {
  const saved = localStorage.getItem('school_config')
  if (saved) {
    config.value = JSON.parse(saved)
  }
  fetchClasses()
  fetchSchoolWeeks()
  fetchSchoolIdentity()
})

const saveConfig = () => {
  if (config.value.daysPerWeek < 1 || config.value.daysPerWeek > 7) return toast.error("BILANGAN HARI MESTI ANTARA 1 HINGGA 7")
  if (config.value.periodsPerDay < 1 || config.value.periodsPerDay > 15) return toast.error("BILANGAN SLOT MASA MESTI ANTARA 1 HINGGA 15")
  
  localStorage.setItem('school_config', JSON.stringify(config.value))
  toast.success("KONFIGURASI SISTEM BERJAYA DISIMPAN!")
}

const fetchClasses = async () => {
  const { data, error } = await supabase
    .from('classes')
    .select('*')
    .order('grade', { ascending: true })
    .order('class_name', { ascending: true })
  
  if (data) classList.value = data
  if (error) console.error(error)
}

const fetchSchoolWeeks = async () => {
  const { data, error } = await supabase
    .from('school_weeks')
    .select('*')
    .order('week_number', { ascending: true })
  
  if (data) allSchoolWeeks.value = data
  if (error) console.error(error)
}

const addClass = async () => {
  if (!newClassName.value.trim()) {
    return toast.error("SILA MASUKKAN NAMA KELAS!")
  }

  try {
    const { error } = await supabase.from('classes').insert({
      grade: newClassGrade.value,
      class_name: newClassName.value.trim().toUpperCase()
    })

    if (error) throw error

    toast.success("KELAS BERJAYA DITAMBAH!")
    newClassName.value = ''
    fetchClasses()
  } catch (err) {
    toast.error("GAGAL MENAMBAH (NAMA KELAS MUNGKIN WUJUD): " + err.message)
  }
}

const deleteClass = async (id) => {
  try {
    const { error } = await supabase.from('classes').delete().eq('id', id)
    if (error) throw error
    toast.success("KELAS TELAH DIPADAM")
    fetchClasses()
  } catch (err) {
    toast.error("GAGAL MEMADAM: " + err.message)
  }
}

const addSchoolWeek = async () => {
  if (!newWeek.value.start_date || !newWeek.value.end_date) {
    return toast.error("SILA ISI TARIKH MULA DAN TAMAT DENGAN LENGKAP!")
  }
  try {
    const { error } = await supabase.from('school_weeks').insert([newWeek.value])
    if (error) throw error
    toast.success("KALENDAR PERSEKOLAHAN BERJAYA DITAMBAH!")
    newWeek.value.week_number += 1 
    newWeek.value.start_date = ''
    newWeek.value.end_date = ''
    fetchSchoolWeeks()
  } catch (err) {
    toast.error("GAGAL MENAMBAH: " + err.message)
  }
}

const deleteSchoolWeek = async (id) => {
  try {
    const { error } = await supabase.from('school_weeks').delete().eq('id', id)
    if (error) throw error
    toast.success("REKOD MINGGU INI TELAH DIPADAM")
    fetchSchoolWeeks()
  } catch (err) {
    toast.error("GAGAL MEMADAM: " + err.message)
  }
}

const downloadWeekTemplate = () => {
  const csvContent = "week_number,start_date,end_date,is_school_week\n" +
                     "1,12/01/2026,16/01/2026,TRUE\n" +
                     "2,19/01/2026,23/01/2026,TRUE\n" +
                     "3,26/01/2026,30/01/2026,FALSE"
  
  const blob = new Blob(["\uFEFF" + csvContent], { type: 'text/csv;charset=utf-8;' })
  const url = URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.setAttribute('href', url)
  link.setAttribute('download', 'TEMPLAT_KALENDAR_PERSEKOLAHAN.csv')
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)
  toast.success("MUAT TURUN TEMPLAT KALENDAR BERJAYA!")
}

const handleWeekFileUpload = async (event) => {
  const file = event.target.files[0]
  if (!file) return

  startProgress('SEDANG MEMBACA FAIL CSV TEMPLAT KALENDAR...')

  const reader = new FileReader()
  reader.onload = async (e) => {
    try {
      await sleep(250)
      updateProgress(35, 'SEDANG MENILIK FORMAT TARIKH DAN KESAHIHAN DATA...')

      const text = e.target.result
      const lines = text.split('\n')
      const rows = []

      const parseDate = (dateStr) => {
        dateStr = dateStr.trim()
        if (dateStr.includes('/')) {
          const parts = dateStr.split('/')
          if (parts.length === 3) {
            return `${parts[2]}-${parts[1].padStart(2, '0')}-${parts[0].padStart(2, '0')}`
          }
        }
        return dateStr
      }

      for (let i = 1; i < lines.length; i++) {
        const line = lines[i].trim()
        if (!line) continue
        const cols = line.split(',')
        if (cols.length >= 4) {
          const rawBool = cols[3].trim().toUpperCase()
          rows.push({
            week_number: Number(cols[0].trim()),
            start_date: parseDate(cols[1]),
            end_date: parseDate(cols[2]),
            is_school_week: rawBool === 'TRUE' || rawBool === '1' || rawBool === 'YES'
          })
        }
      }

      if (rows.length === 0) {
        uploadProgress.value.show = false
        toast.error("FAIL KOSONG ATAU FORMAT SALAH!")
        return
      }

      await sleep(250)
      updateProgress(70, `SEDANG MENULIS ${rows.length} REKOD KALENDAR KE PANGKALAN DATA...`)

      const { error } = await supabase.from('school_weeks').insert(rows)
      if (error) throw error

      await finishProgress(`BERJAYA MENGIMPORT ${rows.length} MINGGU KALENDAR!`)

      toast.success(`BERJAYA MENGIMPORT BERKELOMPOK ${rows.length} REKOD KALENDAR!`)
      if (weekFileInput.value) weekFileInput.value.value = ''
      fetchSchoolWeeks()
    } catch (err) {
      uploadProgress.value.show = false
      toast.error("IMPORT KALENDAR GAGAL: " + err.message)
    }
  }
  reader.readAsText(file)
}

const downloadTemplate = () => {
  const csvContent = "grade,subject_name,planned_periods,kpm_min_hours\n" +
                     "3,BAHASA MELAYU,80,60\n" +
                     "3,MATEMATIK,75,60\n" +
                     "4,SAINS,40,40"
  
  const blob = new Blob(["\uFEFF" + csvContent], { type: 'text/csv;charset=utf-8;' })
  const url = URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.setAttribute('href', url)
  link.setAttribute('download', 'TEMPLAT_SASARAN_SUBJEK.csv')
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)
  toast.success("MUAT TURUN TEMPLAT SASARAN BERJAYA!")
}

const handleFileUpload = async (event) => {
  const file = event.target.files[0]
  if (!file) return

  startProgress('SEDANG MEMBACA FAIL CSV TEMPLAT SASARAN...')

  const reader = new FileReader()
  reader.onload = async (e) => {
    try {
      await sleep(250)
      updateProgress(40, 'SEDANG MENGURAIKAN SASARAN SUBJEK SETIAP TAHUN...')

      const text = e.target.result
      const lines = text.split('\n')
      const rows = []

      for (let i = 1; i < lines.length; i++) {
        const line = lines[i].trim()
        if (!line) continue
        const cols = line.split(',')
        if (cols.length >= 4) {
          rows.push({
            grade: Number(cols[0].trim()),
            subject_name: cols[1].trim(),
            planned_periods: Number(cols[2].trim()),
            kpm_min_hours: Number(cols[3].trim())
          })
        }
      }

      if (rows.length === 0) {
        uploadProgress.value.show = false
        toast.error("FAIL KOSONG ATAU FORMAT SALAH!")
        return
      }

      await sleep(250)
      updateProgress(75, `SEDANG MENULIS ${rows.length} SASARAN SUBJEK KE PANGKALAN DATA...`)

      const { error } = await supabase.from('subject_targets').insert(rows)
      if (error) throw error

      await finishProgress(`BERJAYA MENGIMPORT ${rows.length} SASARAN SUBJEK!`)

      toast.success(`BERJAYA MENGIMPORT BERKELOMPOK ${rows.length} REKOD SASARAN SUBJEK!`)
      if (fileInput.value) fileInput.value.value = ''
    } catch (err) {
      uploadProgress.value.show = false
      toast.error("IMPORT GAGAL: " + err.message)
    }
  }
  reader.readAsText(file)
}

const clearOnlyRecords = async () => {
  if (!confirm("⚠️ ADAKAH ANDA PASTI MAHU MENGKOSONGKAN SEMUA REKOD CUTI & GURU GANTI? TINDAKAN INI TIDAK BOLEH DIBATALKAN!")) return

  loading.value = true
  try {
    const { error: err1 } = await supabase.from('substitute_assignments').delete().not('id', 'is', null)
    if (err1) throw err1

    const { error: err2 } = await supabase.from('leave_requests').delete().not('id', 'is', null)
    if (err2) throw err2

    toast.success("SEMUA REKOD CUTI & GURU GANTI BERJAYA DIKOSONGKAN!")
  } catch (error) {
    toast.error("PENGKOSONGAN GAGAL: " + error.message)
  } finally {
    loading.value = false
  }
}

const clearOnlyTimetable = async () => {
  if (!confirm("⚠️ ADAKAH ANDA PASTI MAHU MENGKOSONGKAN JADUAL WAKTU SEKOLAH? SENARAI GURU AKAN DIKEKALKAN.")) return

  loading.value = true
  try {
    const { error } = await supabase.from('timetable').delete().not('id', 'is', null)
    if (error) throw error

    toast.success("JADUAL WAKTU SEKOLAH TELAH DI TETAP SEMULA!")
  } catch (error) {
    toast.error("PENGKOSONGAN JADUAL GAGAL: " + error.message)
  } finally {
    loading.value = false
  }
}

const backupAndClearMmi = async () => {
  if (!confirm("📥 ADAKAH ANDA PASTI MAHU MEMBUAT SANDARAN DAN MENGKOSONGKAN SEMUA DATA SEJARAH GANGGUAN MMI?")) return

  loading.value = true
  try {
    const { data: mmiData, error: fetchErr } = await supabase.from('mmi_interruptions').select('*')
    if (fetchErr) throw fetchErr

    if (mmiData && mmiData.length > 0) {
      const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(mmiData, null, 2))
      const downloadAnchor = document.createElement('a')
      downloadAnchor.setAttribute("href", dataStr)
      downloadAnchor.setAttribute("download", `MMI_Backup_${new Date().toISOString().slice(0, 10)}.json`)
      document.body.appendChild(downloadAnchor)
      downloadAnchor.click()
      downloadAnchor.remove()
    } else {
      toast.success("TIADA DATA GANGGUAN MMI UNTUK DISANDARKAN, AKAN TERUS KOSONGKAN.")
    }

    const { error: deleteErr } = await supabase.from('mmi_interruptions').delete().not('id', 'is', null)
    if (deleteErr) throw deleteErr

    toast.success("DATA GANGGUAN MMI BERJAYA DISANDARKAN DAN DIKOSONGKAN DARI PANGKALAN DATA!")
  } catch (error) {
    toast.error("SANDARAN ATAU PENGKOSONGAN MMI GAGAL: " + error.message)
  } finally {
    loading.value = false
  }
}

const clearEverything = async () => {
  if (!confirm("🚨 AMARAN: TINDAKAN INI AKAN MEMADAM SEMUA GURU, JADUAL WAKTU, KELAS DAN REKOD CUTI DALAM SISTEM! ADAKAH ANDA PASTI MAHU MENETAP SEMULA KEPADA KEADAAN ASAL?")) return

  loading.value = true
  try {
    await supabase.from('substitute_assignments').delete().not('id', 'is', null)
    await supabase.from('leave_requests').delete().not('id', 'is', null)
    await supabase.from('timetable').delete().not('id', 'is', null)
    await supabase.from('mmi_interruptions').delete().not('id', 'is', null)
    const { error } = await supabase.from('teachers').delete().not('id', 'is', null)
    if (error) throw error

    toast.success("SISTEM TELAH DISET SEMULA SEPENUHNYA KEPADA KEADAAN ASAL!")
    fetchClasses()
    fetchSchoolWeeks()
  } catch (error) {
    toast.error("PENETAPAN SEMULA GAGAL: " + error.message)
  } finally {
    loading.value = false
  }
}

// --- 学校名称与 Logo 设置状态 ---
const schoolNameSetting = ref('')
const schoolLogoSetting = ref('')

const fetchSchoolIdentity = async () => {
  try {
    const { data } = await supabase.from('school_settings').select('*').eq('id', 1).single()
    if (data) {
      schoolNameSetting.value = data.school_name || ''
      schoolLogoSetting.value = data.logo_url || ''
    }
  } catch (err) {
    schoolNameSetting.value = localStorage.getItem('school_name') || ''
    schoolLogoSetting.value = localStorage.getItem('school_logo') || ''
  }
}

const handleSettingsLogoUpload = (event) => {
  const file = event.target.files[0]
  if (!file) return

  if (file.size > 2 * 1024 * 1024) {
    return toast.error("SAIZ IMEJ LOGO TERLALU BESAR, SILA PILIH BAWAH 2MB")
  }

  const reader = new FileReader()
  reader.onload = (e) => {
    schoolLogoSetting.value = e.target.result
    toast.success("LOGO BAHARU BERJAYA DIBACA, SILA KLIK SIMPAN DI BAWAH!")
  }
  reader.readAsDataURL(file)
}

const saveSchoolIdentity = async () => {
  try {
    const { error } = await supabase
      .from('school_settings')
      .update({
        school_name: schoolNameSetting.value.trim(),
        logo_url: schoolLogoSetting.value
      })
      .eq('id', 1)

    if (error) throw error

    localStorage.setItem('school_logo', schoolLogoSetting.value)
    localStorage.setItem('school_name', schoolNameSetting.value.trim())

    toast.success("LOGO & NAMA SEKOLAH BERJAYA DIKEMASKINI! SISTEM TELAH DIKEMASKINI.")

    setTimeout(() => {
      window.location.reload()
    }, 600)
  } catch (err) {
    toast.error("GAGAL MENYIMPAN: " + err.message)
  }
}

// 📦 导出全量系统备份（Supabase 云端版）
const exportFullBackup = async () => {
  try {
    toast.success("SEDANG MENYEDIAKAN SANDARAN DATA CLOUD...")
    
    const [teachers, classes, schoolWeeks, subjectTargets, timetable, leaveRequests, substituteAssignments, schoolSettings] = await Promise.all([
      supabase.from('teachers').select('*'),
      supabase.from('classes').select('*'),
      supabase.from('school_weeks').select('*'),
      supabase.from('subject_targets').select('*'),
      supabase.from('timetable').select('*'),
      supabase.from('leave_requests').select('*'),
      supabase.from('substitute_assignments').select('*'),
      supabase.from('school_settings').select('*')
    ])

    const backupPackage = {
      version: "2.0-supabase",
      exportDate: new Date().toISOString(),
      data: {
        teachers: teachers.data || [],
        classes: classes.data || [],
        schoolWeeks: schoolWeeks.data || [],
        subjectTargets: subjectTargets.data || [],
        timetable: timetable.data || [],
        leaveRequests: leaveRequests.data || [],
        substituteAssignments: substituteAssignments.data || [],
        schoolSettings: schoolSettings.data || []
      }
    }

    const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(backupPackage, null, 2))
    const downloadAnchor = document.createElement('a')
    downloadAnchor.setAttribute("href", dataStr)
    downloadAnchor.setAttribute("download", `SJKC_Supabase_Backup_${new Date().toISOString().slice(0, 10)}.json`)
    document.body.appendChild(downloadAnchor)
    downloadAnchor.click()
    downloadAnchor.remove()

    toast.success("EKSPORT SANDARAN PENUH SISTEM BERJAYA!")
  } catch (err) {
    toast.error("EKSPORT SANDARAN GAGAL: " + err.message)
  }
}

// 📥 恢复全量系统备份（WebApp 云端专用 - 兼容本地与云端JSON）
const importFullBackup = async (event) => {
  const file = event.target.files[0]
  if (!file) return

  if (!confirm("⚠️ AMARAN: PEMULIHAN SANDARAN AKAN MENIMPA DATA SEMASA DALAM PANGKALAN DATA CLOUD! ADAKAH ANDA PASTI?")) {
    event.target.value = ''
    return
  }

  toast.info("SEDANG MEMBACA DAN MENILIK FAIL SANDARAN...")
  const reader = new FileReader()
  
  reader.onload = async (e) => {
    try {
      const raw = JSON.parse(e.target.result)
      let d = {}

      if (raw.data) {
        d = raw.data 
      } else if (typeof raw === 'object' && !Array.isArray(raw)) {
        d = raw      
      } else {
        throw new Error("FORMAT FAIL TIDAK SOKONG")
      }
      
      if (Array.isArray(d.schoolSettings) && d.schoolSettings.length > 0) {
        await supabase.from('school_settings').upsert(d.schoolSettings)
      }
      
      if (Array.isArray(d.classes) && d.classes.length > 0) {
        await supabase.from('classes').upsert(d.classes)
      }

      if (Array.isArray(d.teachers) && d.teachers.length > 0) {
        const cleanTeachers = d.teachers.map(t => ({
          id: t.id,
          name: t.name,
          max_substitute_per_week: t.max_substitute_per_week || t.max_lessons_per_day || 6,
          is_active: t.is_active ?? true,
          session: t.session || 'morning'
        }))
        await supabase.from('teachers').upsert(cleanTeachers)
      }

      if (Array.isArray(d.schoolWeeks) && d.schoolWeeks.length > 0) {
        const cleanWeeks = d.schoolWeeks.map(w => ({
          ...w,
          is_school_week: (w.is_school_week === true || w.is_school_week === 1 || w.is_school_week === 'true')
        }))
        await supabase.from('school_weeks').upsert(cleanWeeks)
      }

      if (Array.isArray(d.subjectTargets) && d.subjectTargets.length > 0) {
        await supabase.from('subject_targets').upsert(d.subjectTargets)
      }

      if (Array.isArray(d.timetable) && d.timetable.length > 0) {
        const cleanTimetable = d.timetable.map(tt => ({
          id: tt.id,
          teacher_id: tt.teacher_id,
          class_name: tt.class_name || tt.class_id, 
          subject: tt.subject || tt.subject_name,
          weekday: tt.weekday || tt.day_of_week,
          period: tt.period || tt.period_number
        }))
        await supabase.from('timetable').upsert(cleanTimetable)
      }

      if (Array.isArray(d.leaveRequests) && d.leaveRequests.length > 0) {
        await supabase.from('leave_requests').upsert(d.leaveRequests)
      }
      if (Array.isArray(d.substituteAssignments) && d.substituteAssignments.length > 0) {
        await supabase.from('substitute_assignments').upsert(d.substituteAssignments)
      }
      if (Array.isArray(d.mmiInterruptions) && d.mmiInterruptions.length > 0) {
        await supabase.from('mmi_interruptions').upsert(d.mmiInterruptions)
      }

      toast.success("PEMULIHAN DATA SISTEM BERJAYA! LAMAN AKAN DIREFRESH...")
      setTimeout(() => {
        window.location.reload()
      }, 1000)
      
    } catch (err) {
      console.error("RALAT IMPORT:", err)
      toast.error("PEMULIHAN GAGAL: " + err.message)
    } finally {
      event.target.value = '' 
    }
  }
  
  reader.readAsText(file)
}
</script>