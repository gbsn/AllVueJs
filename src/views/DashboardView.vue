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
      
      <button @click="$router.push('/status')" class="btn-link btn-navigation">🚀 Ver Status Global do GitHub</button>
      <br>
      <a :to="userData.html_url" target="_blank" class="btn-link">Ver GitHub Oficial</a>
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

// Função para buscar os dados do seu GitHub
const fetchGitHubData = async () => {
  try {
    // Substitua 'oQuasi' pelo seu nome de usuário exato no GitHub
    const response = await axios.get('https://api.github.com/users/gbsn')
    userData.value = response.data
  } catch (error) {
    console.error("Erro ao buscar dados:", error)
  }
}

// Quando a tela abre, ela executa a busca
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

.btn-link {
  display: inline-block;
  margin-top: 20px;
  color: #1a1a1a;
  background-color: #42d392;
  padding: 10px 20px;
  border-radius: 8px;
  text-decoration: none;
  font-weight: bold;
}

.btn-navigation {
  margin-top: 10px; /* Pequeno espaço entre os botões */
  background: linear-gradient(135deg, var(--dynamic-color), #647eff); /* Mesmo degradê do login/botão oficial */
  cursor: pointer;
  border: none;
  width: 100%; /* Para ocupar a mesma largura no mobile */
}

.btn-navigation:hover {
  filter: brightness(1.2);
  box-shadow: 0 0 15px var(--dynamic-color);
}
</style>