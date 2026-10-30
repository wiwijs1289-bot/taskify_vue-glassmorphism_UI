<script setup>
import { ref, computed } from 'vue'

const tasks = ref([
  // { id: 1, title: 'Tugas MTK', status: false },
  // { id: 2, title: 'Tugas PPKN', status: false },
  // { id: 3, title: 'Tugas Sejarah', status: false },
  // { id: 4, title: 'Proyek Desain UI', status: false },
  // { id: 5, title: 'Tugas Piket', status: false },
  // { id: 6, title: 'Jalan Jalan', status: false },
  // { id: 7, title: 'Tugas IPA', status: false },
  // { id: 8, title: 'Proyek Akhir UI', status: false }
])

const inputTask = ref('')
const searchQuery = ref('')
const filterType = ref('all') // 'all', 'completed', 'active'

// Theme State
const isDark = ref(false)
const toggleTheme = () => {
  isDark.value = !isDark.value
}

// Edit State
const editingId = ref(null)
const editingTitle = ref('')

const startEdit = (task) => {
  editingId.value = task.id
  editingTitle.value = task.title
}

const saveEdit = (task) => {
  if (editingTitle.value.trim() !== '') {
    task.title = editingTitle.value.trim()
  }
  editingId.value = null
}

const cancelEdit = () => {
  editingId.value = null
}

const addTask = (title) => {
  if (!title.trim()) return
  const newTask = {
    id: Date.now(),
    title: title.trim(),
    status: false
  }
  tasks.value.push(newTask)
  inputTask.value = ''
}

const deleteTask = (id) => {
  if (confirm('Anda yakin ingin menghapus tugas ini?')) {
    tasks.value = tasks.value.filter(item => item.id !== id)
  }
}

const clearAll = () => {
  if (confirm('Apakah Anda yakin ingin menghapus semua tugas?')) {
    tasks.value = []
  }
}

// Computed statistik tugas
const totalTasksCount = computed(() => tasks.value.length)
const completedTasksCount = computed(() => tasks.value.filter(t => t.status).length)
const progressPercentage = computed(() => {
  if (totalTasksCount.value === 0) return 0
  return Math.round((completedTasksCount.value / totalTasksCount.value) * 100)
})

const filteredTasks = computed(() => {
  return tasks.value.filter(task => {
    const matchesSearch = task.title.toLowerCase().includes(searchQuery.value.toLowerCase())
    if (filterType.value === 'completed') return matchesSearch && task.status
    if (filterType.value === 'active') return matchesSearch && !task.status
    return matchesSearch
  })
})
</script>

<template>
  <!-- Main Background Container with Dynamic Gradient based on Mode -->
  <div 
    class="min-h-screen w-full transition-colors duration-500 p-4 md:p-12 flex items-center justify-center font-sans"
    :class="[
      isDark 
        ? 'bg-gradient-to-tr from-slate-950 via-purple-950 to-slate-900 text-slate-100' 
        : 'bg-gradient-to-tr from-yellow-100 via-pink-200 to-sky-300 text-slate-800'
    ]"
  >
    
    <!-- Glass Window Container -->
    <main 
      class="w-full max-w-4xl rounded-3xl border backdrop-blur-xl shadow-2xl backdrop-saturate-150 transition-all duration-300 p-6 md:p-8"
      :class="[
        isDark 
          ? 'border-white/10 bg-slate-900/40 shadow-purple-950/30' 
          : 'border-white/40 bg-white/20'
      ]"
    >
      
      <!-- Header Bar -->
      <header class="flex items-center justify-between gap-4 mb-8">
        <!-- Logo & Title -->
        <div class="flex items-center gap-3">
          <div class="flex h-9 w-9 items-center justify-center rounded-xl bg-gradient-to-br from-indigo-500 via-purple-500 to-pink-500 text-white shadow-md">
            ✦
          </div>
          <h1 
            class="text-xl font-bold tracking-tight font-advent-pro"
            :class="isDark ? 'text-white' : 'text-slate-800'"
          >
            Taskify Vue
          </h1>
        </div>

        <!-- Search Bar -->
        <div class="relative flex-1 max-w-md hidden sm:block">
          <span class="absolute inset-y-0 left-3 flex items-center text-slate-400 text-sm">
            🔍
          </span>
          <input 
            v-model="searchQuery" 
            type="text" 
            placeholder="Search" 
            class="w-full rounded-full border py-2 pl-9 pr-4 text-sm backdrop-blur-md outline-none transition placeholder-slate-400"
            :class="[
              isDark 
                ? 'border-white/10 bg-slate-800/40 text-slate-100 focus:border-purple-400 focus:bg-slate-800/60' 
                : 'border-white/50 bg-white/30 text-slate-700 focus:border-white focus:bg-white/50'
            ]"
          />
        </div>

        <!-- Right Side Controls: Dark/Light Toggle + Avatar -->
        <div class="flex items-center gap-3">
          <!-- Dark/Light Mode Toggle Button -->
          <button 
            @click="toggleTheme" 
            class="flex h-10 w-10 items-center justify-center rounded-full border transition backdrop-blur-md active:scale-95 shadow-sm"
            :class="[
              isDark 
                ? 'border-white/20 bg-purple-800/60 text-red-300 hover:bg-slate-700/60' 
                : 'border-white/60 bg-white/40 text-slate-700 hover:bg-white/60'
            ]"
            :title="isDark ? 'Switch to Light Mode' : 'Switch to Dark Mode'"
          >
            <span v-if="isDark" class="text-base">☀️</span>
            <span v-else class="text-base">🌙</span>
          </button>

          <!-- Avatar -->
          <div class="h-10 w-10 overflow-hidden rounded-full border-2 border-white/60 shadow-sm">
            <img src="https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&q=80&w=150" alt="Avatar" class="h-full w-full object-cover" />
          </div>
        </div>
      </header>

      <!-- Form Tambah Tugas -->
      <section 
        class="mb-6 rounded-2xl border p-5 backdrop-blur-md shadow-sm transition"
        :class="[
          isDark 
            ? 'border-white/10 bg-slate-800/30' 
            : 'border-white/40 bg-white/30'
        ]"
      >
        <h2 
          class="mb-3 text-base font-semibold"
          :class="isDark ? 'text-slate-200' : 'text-slate-800'"
        >
          Tambah Tugas
        </h2>
        <form @submit.prevent="addTask(inputTask)" class="flex gap-3">
          <input 
            v-model="inputTask" 
            type="text" 
            placeholder="Masukkan Tugas Baru..." 
            class="flex-1 rounded-xl border px-4 py-3 text-sm outline-none backdrop-blur-md transition placeholder-slate-400"
            :class="[
              isDark 
                ? 'border-white/10 bg-slate-900/50 text-slate-100 focus:border-purple-400 focus:bg-slate-900/80' 
                : 'border-white/40 bg-white/40 text-slate-800 focus:border-white focus:bg-white/60'
            ]"
          />
          <button 
            type="submit" 
            class="flex items-center gap-1 rounded-xl px-6 py-3 text-sm font-fjalla-one backdrop-blur-md border border-white/40 shadow-sm transition active:scale-95"
            :class="[
              isDark 
                ? 'bg-purple-600/80 hover:bg-purple-500 text-white' 
                : 'bg-teal-400/80 hover:bg-teal-300 text-slate-800'
            ]"
          >
            ADD <span class="text-lg leading-none">+</span>
          </button>
        </form>
      </section>

      <!-- Section Bar Progress & Persentase Tugas -->
      <section 
        class="mb-8 rounded-2xl border p-4 backdrop-blur-md shadow-sm transition"
        :class="[
          isDark 
            ? 'border-white/10 bg-slate-800/20' 
            : 'border-white/40 bg-white/30'
        ]"
      >
        <div class="flex items-center justify-between text-xs md:text-sm font-medium mb-2">
          <span :class="isDark ? 'text-slate-300' : 'text-slate-700'">
            Progress Tugas: <strong class="text-teal-600 dark:text-teal-400">{{ completedTasksCount }}</strong> dari <strong>{{ totalTasksCount }}</strong> Selesai
          </span>
          <span class="font-bold text-teal-600 dark:text-teal-400">
            {{ progressPercentage }}%
          </span>
        </div>
        <!-- Indikator Progress Bar -->
        <div class="w-full h-2.5 rounded-full overflow-hidden bg-black/5 dark:bg-white/10 border border-white/20">
          <div 
            class="h-full bg-gradient-to-r from-teal-400 to-sky-400 transition-all duration-500 rounded-full"
            :style="{ width: `${progressPercentage}%` }"
          ></div>
        </div>
      </section>

      <!-- List Tugas dengan Support Edit -->
      <section class="space-y-3 mb-8" v-auto-animate>
        <div 
          v-for="task in filteredTasks" 
          :key="task.id" 
          class="flex items-center justify-between rounded-xl border px-5 py-4 backdrop-blur-md transition"
          :class="[
            isDark 
              ? 'border-white/10 bg-slate-800/20 hover:bg-slate-800/40' 
              : 'border-white/40 bg-white/30 hover:bg-white/40'
          ]"
        >
          <!-- Mode Edit Form -->
          <template v-if="editingId === task.id">
            <div class="flex items-center gap-2 flex-1 mr-4">
              <input 
                v-model="editingTitle" 
                type="text" 
                @keyup.enter="saveEdit(task)"
                @keyup.esc="cancelEdit"
                class="w-full rounded-lg border px-3 py-1.5 text-sm outline-none backdrop-blur-md"
                :class="[
                  isDark 
                    ? 'border-purple-500/50 bg-slate-900/80 text-white' 
                    : 'border-white/60 bg-white/60 text-slate-800'
                ]"
                autofocus
              />
              <button 
                @click="saveEdit(task)" 
                class="rounded-lg bg-emerald-500/80 px-3 py-1.5 text-xs font-semibold text-white hover:bg-emerald-600 transition"
              >
                Simpan
              </button>
              <button 
                @click="cancelEdit" 
                class="rounded-lg bg-slate-500/50 px-3 py-1.5 text-xs font-semibold text-slate-200 hover:bg-slate-500/70 transition"
              >
                Batal
              </button>
            </div>
          </template>

          <!-- Mode Tampilan Normal -->
          <template v-else>
            <label class="flex items-center gap-3 cursor-pointer select-none flex-1">
              <input 
                type="checkbox" 
                v-model="task.status" 
                class="h-4 w-4 rounded border-slate-300 text-teal-600 focus:ring-teal-500 cursor-pointer"
              />
              <span 
                :class="[
                  task.status 
                    ? 'line-through text-slate-500 font-balsamiq-sans' 
                    : isDark ? 'text-slate-100 font-balsamiq-sans' : 'text-gray-900 font-balsamiq-sans'
                ]" 
                class="text-sm"
              >
                {{ task.title }}
              </span>
            </label>

            <div class="flex items-center gap-3">
              <!-- Tombol Edit -->
              <button 
                @click="startEdit(task)" 
                class="flex items-center gap-1 text-xs font-semibold transition"
                :class="isDark ? 'text-sky-400 hover:text-sky-300' : 'text-blue-600 hover:text-blue-700'"
              >
                Edit ✏️
              </button>

              <!-- Tombol Hapus -->
              <button 
                @click="deleteTask(task.id)" 
                class="flex items-center gap-1 text-xs font-semibold text-rose-500/80 hover:text-rose-600 transition"
              >
                Hapus <span class="text-sm">✕</span>
              </button>
            </div>
          </template>
        </div>

        <div v-if="filteredTasks.length === 0" class="text-center py-6 text-slate-400 text-sm">
          Tidak ada tugas.
        </div>
      </section>

      <!-- Footer / Filters -->
      <footer class="flex flex-wrap items-center gap-2 pt-2 border-t" :class="isDark ? 'border-white/10' : 'border-white/20'">
        <button 
          @click="filterType = 'all'"
          :class="[
            filterType === 'all' 
              ? (isDark ? 'bg-white/30 text-white font-semibold' : 'bg-white/60 text-slate-800 font-semibold') 
              : (isDark ? 'bg-white/10 text-slate-300' : 'bg-white/20 text-slate-600')
          ]"
          class="rounded-xl border border-white/20 px-4 py-2 text-xs backdrop-blur-md transition hover:bg-white/30"
        >
          ≡ Filter: Semua
        </button>
        <button 
          @click="filterType = 'active'"
          :class="[
            filterType === 'active' 
              ? (isDark ? 'bg-white/30 text-white font-semibold' : 'bg-white/60 text-slate-800 font-semibold') 
              : (isDark ? 'bg-white/10 text-slate-300' : 'bg-white/20 text-slate-600')
          ]"
          class="rounded-xl border border-white/20 px-4 py-2 text-xs backdrop-blur-md transition hover:bg-white/30"
        >
          ⇣̂ Urutkan: Tanggal
        </button>
        <button 
          @click="filterType = 'completed'"
          :class="[
            filterType === 'completed' 
              ? (isDark ? 'bg-white/30 text-white font-semibold' : 'bg-white/60 text-slate-800 font-semibold') 
              : (isDark ? 'bg-white/10 text-slate-300' : 'bg-white/20 text-slate-600')
          ]"
          class="rounded-xl border border-white/20 px-4 py-2 text-xs backdrop-blur-md transition hover:bg-white/30"
        >
          ✓ Selesai
        </button>
        <button 
          @click="clearAll"
          class="rounded-xl border border-white/20 bg-white/10 px-4 py-2 text-xs text-rose-500 backdrop-blur-md transition hover:bg-rose-500/20 ml-auto"
        >
          🗑 Hapus Semua
        </button>
      </footer>

    </main>
  </div>
</template>