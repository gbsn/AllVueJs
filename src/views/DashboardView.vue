<template>
  <div class="dashboard-container">
    <header>
      <h1>📊 Perfil GitHub</h1>
      <button @click="logout" class="btn-logout">Sair</button>
    </header>

    <div class="search-box">
      <input 
        v-model="usernameInput" 
        type="text" 
        placeholder="Digite o usuário do GitHub..."
        @keyup.enter="searchUser"
      />
      <button @click="searchUser" class="btn-search">Consultar</button>
    </div>

    <div class="timer-info">
      <small>Sincronizando em: <strong>{{ timeLeft }}s</strong></small>
      <div class="progress-bg">
        <div class="progress-bar" :style="{ width: (timeLeft / 30) * 100 + '%' }"></div>
      </div>
    </div>

    <div v-if="userData" class="profile-card">
      <img :src="userData.avatar_url" alt="Avatar" class="avatar" />
      <h2>{{ userData.name || userData.login }}</h2>
      <p class="bio">{{ userData.bio }}</p>
      
      <div class="stats">
        <div class="stat-item">
          <span class="label">Seguidores</span>
          <span class="value">{{ userData.followers }}</span>
        </div>
        <div class="stat-item">
          <span class="label">Repositórios</span>
          <span class="value">{{ userData.public_repos }}</span>
        </div>
      </div>
      
      <div class="actions-container">
        <button @click="$router.push('/status?popup=welcome')" class="btn-link btn-navigation">
          🚀 Ver Status Global do GitHub
        </button>
        
        <a :href="userData.html_url" target="_blank" class="btn-link">
          Ver GitHub Oficial
        </a>
      </div>
    </div>

    <p v-else class="loading">Buscando dados do sistema...</p>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

const router = useRouter()
const userData = ref(null)
const usernameInput = ref('gbsn') // Usuário inicial
const timeLeft = ref(30)
let timerInterval = null

// Função para buscar dados (Tarefa 2)
const fetchGitHubData = async (user) => {
  try {
    const response = await axios.get(`https://api.github.com/users/${user}`)
    userData.value = response.data
  } catch (error) {
    alert("Usuário não encontrado!")
    console.error("Erro ao buscar dados:", error)
  }
}

// Lógica de busca manual
const searchUser = () => {
  if (usernameInput.value.trim() !== '') {
    fetchGitHubData(usernameInput.value)
    timeLeft.value = 30 // Reseta o timer na busca manual
  }
}

// Tarefa 3: Temporizador de 30 segundos
const startTimer = () => {
  timerInterval = setInterval(() => {
    if (timeLeft.value > 0) {
      timeLeft.value--
    } else {
      fetchGitHubData(usernameInput.value)
      timeLeft.value = 30
    }
  }, 1000)
}

onMounted(() => {
  fetchGitHubData(usernameInput.value)
  startTimer()
})

onUnmounted(() => {
  if (timerInterval) clearInterval(timerInterval)
})

const logout = () => router.push('/')
</script>

<style scoped>
.dashboard-container {
  max-width: 600px;
  margin: 20px auto;
  padding: 20px;
  color: #bd93f9;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  border-bottom: 1px solid #42d392;
  padding-bottom: 10px;
}

/* Estilo da Busca (Tarefas 1 e 2) */
.search-box {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.search-box input {
  flex: 1;
  background: #1a1a1a;
  border: 1px solid #42d392;
  padding: 10px;
  border-radius: 5px;
  color: white;
}

.btn-search {
  background: #42d392;
  border: none;
  padding: 0 15px;
  border-radius: 5px;
  font-weight: bold;
  cursor: pointer;
}

/* Estilo do Timer (Tarefa 3) */
.timer-info {
  margin-bottom: 20px;
}

.progress-bg {
  height: 4px;
  background: rgba(255,255,255,0.1);
  margin-top: 5px;
  border-radius: 2px;
}

.progress-bar {
  height: 100%;
  background: #42d392;
  box-shadow: 0 0 10px #42d392;
  transition: width 1s linear;
}

.profile-card {
  background-color: #1a1a1a;
  border: 1px solid #42d392;
  padding: 30px;
  border-radius: 15px;
  text-align: center;
  box-shadow: 0 0 15px rgba(66, 211, 146, 0.1);
}

.avatar {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  border: 3px solid #42d392;
  margin-bottom: 15px;
}

.stats {
  display: flex;
  justify-content: space-around;
  margin: 25px 0;
}

.value {
  font-size: 1.5em;
  font-weight: bold;
  color: #42d392;
}

.actions-container {
  display: flex;
  flex-direction: column;
  gap: 12px;
  margin-top: 20px;
}

.btn-link {
  display: block;
  width: 100%;
  padding: 12px;
  border-radius: 8px;
  text-decoration: none;
  font-weight: bold;
  background-color: #42d392;
  color: #1a1a1a;
  border: none;
  cursor: pointer;
}

.btn-navigation {
  background: linear-gradient(135deg, #42d392, #647eff) !important;
  color: #1a1a1a !important;
}

.btn-logout {
  background: transparent;
  color: #ff5555;
  border: 1px solid #ff5555;
  padding: 5px 15px;
  border-radius: 5px;
  cursor: pointer;
}
</style>