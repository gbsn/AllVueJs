<template>
  <div class="dashboard-container" :style="{ '--dynamic-color': mainColor }">
    <header>
      <h1>📊 Perfil GitHub</h1>
      <button @click="logout" class="btn-logout">Sair</button>
    </header>

    <div v-if="userData" class="profile-card">
      <img 
        :src="userData.avatar_url" 
        alt="Avatar" 
        class="avatar" 
        crossOrigin="anonymous" 
      />
      
      <h2>{{ userData.name || userData.login }}</h2>
      <p class="bio">{{ userData.bio }}</p>
      
      <div class="stats">
        <div class="stat-item">
          <span class="value">{{ userData.followers }}</span>
          <span class="label">Seguidores</span>
        </div>
        <div class="stat-item">
          <span class="value">{{ userData.public_repos }}</span>
          <span class="label">Repositórios</span>
        </div>
      </div>

      <div v-if="userRepos.length" class="repos-section">
        <h3>📂 Meus Projetos (Recentes)</h3>
        <ul>
          <li v-for="repo in userRepos.slice(0, 4)" :key="repo.id">
            <a :href="repo.html_url" target="_blank">{{ repo.name }}</a>
            <span class="stars">⭐ {{ repo.stargazers_count }}</span>
          </li>
        </ul>
      </div>
      
      <a :href="userData.html_url" target="_blank" class="btn-link">Ver GitHub Completo</a>
    </div>

    <p v-else class="loading">Iniciando protocolos de busca...</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

const router = useRouter()
const userData = ref(null)
const userRepos = ref([]) // Armazena a lista de projetos
const mainColor = ref('#42d392') // Verde Vue como fallback

const fetchGitHubData = async () => {
  try {
    // 1. Busca os dados do Perfil
    const profileRes = await axios.get('https://api.github.com/users/gbsn')
    userData.value = profileRes.data

    // 2. Busca os Repositórios (ordenados pelos mais recentes)
    const reposRes = await axios.get('https://api.github.com/users/gbsn/repos?sort=updated')
    userRepos.value = reposRes.data
  } catch (error) {
    console.error("Erro na integração com GitHub API:", error)
  }
}

const extractColor = () => {
  const script = document.createElement('script')
  script.src = "https://cdnjs.cloudflare.com/ajax/libs/color-thief/2.3.0/color-thief.umd.js"
  
  script.onload = () => {
    const img = document.querySelector('.avatar')
    if (!img) return

    // eslint-disable-next-line no-undef
    const colorThief = new ColorThief()
    
    const applyColor = () => {
      try {
        const color = colorThief.getColor(img)
        mainColor.value = `rgb(${color[0]}, ${color[1]}, ${color[2]})`
      } catch (e) {
        console.log("Processando imagem...")
      }
    }

    if (img.complete) {
      applyColor()
    } else {
      img.addEventListener('load', applyColor)
    }
  }
  document.head.appendChild(script)
}

onMounted(async () => {
  await fetchGitHubData()
  // Tempo para o Vue renderizar a foto antes de ler os pixels
  setTimeout(extractColor, 800)
})

const logout = () => router.push('/')
</script>

<style scoped>
.dashboard-container {
  min-height: 100vh;
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  color: #bd93f9;
  background: radial-gradient(circle at center, rgba(0,0,0,0) 0%, var(--dynamic-color) 250%);
  transition: background 1s ease;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 25px;
  border-bottom: 1px solid var(--dynamic-color);
  padding-bottom: 10px;
}

.profile-card {
  background-color: #1a1a1a;
  border: 2px solid var(--dynamic-color); 
  padding: 25px;
  border-radius: 15px;
  text-align: center;
  box-shadow: 0 0 25px var(--dynamic-color);
  transition: all 0.6s ease;
}

.avatar {
  width: 110px;
  height: 110px;
  border-radius: 50%;
  border: 3px solid var(--dynamic-color);
  margin-bottom: 15px;
  object-fit: cover;
}

.bio {
  font-size: 0.9rem;
  margin: 10px 0;
  color: #ccc;
  line-height: 1.4;
}

.stats {
  display: flex;
  justify-content: space-around;
  margin: 20px 0;
  padding: 15px 0;
  border-top: 1px solid rgba(255,255,255,0.1);
  border-bottom: 1px solid rgba(255,255,255,0.1);
}

.stat-item {
  display: flex;
  flex-direction: column;
}

.value {
  font-size: 1.4rem;
  font-weight: 800;
  color: var(--dynamic-color);
}

.label {
  font-size: 0.75rem;
  text-transform: uppercase;
  color: #888;
}

.repos-section {
  margin-top: 20px;
  text-align: left;
}

.repos-section h3 {
  font-size: 0.9rem;
  color: var(--dynamic-color);
  margin-bottom: 12px;
}

ul { list-style: none; padding: 0; }

li {
  display: flex;
  justify-content: space-between;
  background: rgba(255,255,255,0.05);
  margin-bottom: 6px;
  padding: 10px;
  border-radius: 6px;
  border-left: 3px solid var(--dynamic-color);
}

li a {
  color: #bd93f9;
  text-decoration: none;
  font-size: 0.85rem;
  font-weight: 600;
}

.stars {
  font-size: 0.8rem;
  color: #888;
}

.btn-link {
  display: block;
  margin-top: 20px;
  color: #1a1a1a;
  background-color: var(--dynamic-color);
  padding: 12px;
  border-radius: 8px;
  text-decoration: none;
  font-weight: bold;
  text-transform: uppercase;
  transition: transform 0.2s;
}

.btn-link:hover {
  transform: translateY(-2px);
}

.btn-logout {
  background: transparent;
  color: #ff5555;
  border: 1px solid #ff5555;
  padding: 5px 15px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 0.8rem;
}

.loading {
  text-align: center;
  margin-top: 50px;
  font-style: italic;
  color: var(--dynamic-color);
}
</style>