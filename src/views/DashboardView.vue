<template>
  <div class="dashboard-container">
    <header>
      <h1>📊 Perfil GitHub</h1>
      <button @click="logout" class="btn-logout">Sair</button>
    </header>

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
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

const router = useRouter()
const userData = ref(null)

const fetchGitHubData = async () => {
  try {
    const response = await axios.get('https://api.github.com/users/gbsn')
    userData.value = response.data
  } catch (error) {
    console.error("Erro ao buscar dados:", error)
  }
}

onMounted(() => {
  fetchGitHubData()
})

const logout = () => {
  router.push('/')
}
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
  margin-bottom: 30px;
  border-bottom: 1px solid #42d392;
  padding-bottom: 10px;
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

.stat-item {
  display: flex;
  flex-direction: column;
}

.value {
  font-size: 1.5em;
  font-weight: bold;
  color: #42d392;
}

.btn-logout {
  background: transparent;
  color: #ff5555;
  border: 1px solid #ff5555;
  padding: 5px 15px;
  border-radius: 5px;
  cursor: pointer;
}

/* AJUSTES DOS BOTÕES */
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
  font-size: 0.9rem;
  border: none;
  cursor: pointer;
  background-color: #42d392;
  color: #1a1a1a;
  transition: 0.3s;
}

.btn-navigation {
  /* Forçamos o degradê e garantimos que o texto não suma */
  background: linear-gradient(135deg, #42d392, #647eff) !important;
  color: #1a1a1a !important;
  box-shadow: 0 0 10px rgba(66, 211, 146, 0.3);
}

.btn-link:hover {
  filter: brightness(1.2);
  transform: translateY(-2px);
  box-shadow: 0 0 15px #42d392;
}

.loading {
  text-align: center;
  color: #42d392;
}
</style>