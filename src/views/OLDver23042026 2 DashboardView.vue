<template>
  <div class="dashboard-container" :style="{ '--dynamic-color': mainColor }">
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
      
      <a :to="userData.html_url" target="_blank" class="btn-link">Ver GitHub Oficial</a>
    </div>

    <p v-else class="loading">Buscando dados do sistema...</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'
// Removemos a linha do import ColorThief daqui

/* OLDver 23042026
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'
import ColorThief from 'colorthief' // A biblioteca mágica
*/

const router = useRouter()
const userData = ref(null)
const mainColor = ref('#1a1a1a') // Cor padrão inicial (preto)

const fetchGitHubData = async () => {
  try {
    const response = await axios.get('https://api.github.com/users/gbsn')
    userData.value = response.data
  } catch (error) {
    console.error("Erro ao buscar dados:", error)
  }
}

// Função que extrai a cor da foto
const extractColor = () => {
  // Criamos o script da biblioteca dinamicamente para não dar erro de importação
  const script = document.createElement('script')
  script.src = "https://cdnjs.cloudflare.com/ajax/libs/color-thief/2.3.0/color-thief.umd.js"
  
  script.onload = () => {
    const img = document.querySelector('.avatar')
    if (!img) return

    // @ts-ignore - ColorThief estará disponível globalmente após o load do script
    const colorThief = new ColorThief()
    
    const applyColor = () => {
      try {
        const color = colorThief.getColor(img)
        mainColor.value = `rgb(${color[0]}, ${color[1]}, ${color[2]})`
      } catch (e) {
        console.log("Aguardando carregamento da imagem...")
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
  // Pequeno delay para garantir que o elemento <img> apareceu no HTML
  setTimeout(extractColor, 500)
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
  margin-bottom: 30px;
  border-bottom: 1px solid #42d392;
  padding-bottom: 10px;
}

.profile-card {
  background-color: #1a1a1a;
  /* Agora a borda e o brilho usam a cor da sua foto! */
  border: 2px solid var(--dynamic-color); 
  padding: 30px;
  border-radius: 15px;
  text-align: center;
  box-shadow: 0 0 30px var(--dynamic-color); /* Brilho neon da cor da foto */
  transition: all 0.5s ease; /* Transição suave */
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

.value, .btn-link {
  color: #1a1a1a;
  background-color: var(--dynamic-color); /* Botão muda de cor também! */
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

/* Se quiser que o fundo da tela toda mude levemente: */
.dashboard-container {
  min-height: 100vh;
  background: radial-gradient(circle at center, rgba(0,0,0,0) 0%, var(--dynamic-color) 200%);
}

</style>