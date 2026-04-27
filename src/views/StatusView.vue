<template>
  <div class="status-container" :style="{ '--dynamic-color': '#42d392' }">
    <header>
      <h1>📡 System Status</h1>
      <div class="header-actions">
        <button @click="refreshData" class="btn-refresh" title="Atualizar Agora">🔄</button>
        <button @click="$router.push('/dashboard')" class="btn-logout">Voltar</button>
      </div>
    </header>

    <div v-if="showWelcomeModal" class="modal-overlay">
      <div class="modal-content">
        <h2>👋 Bem-vindo ao Monitor</h2>
        <p>Você acessou via link especial de boas-vindas.</p>
        <p>Acompanhe o status operacional dos serviços do GitHub.</p>
        <button @click="showWelcomeModal = false" class="btn-link">Explorar Painel</button>
      </div>
    </div>

    <div class="timer-bar">
      <small>Próxima atualização em: <strong>{{ timeLeft }}s</strong></small>
      <div class="progress" :style="{ width: (timeLeft / 30) * 100 + '%' }"></div>
    </div>

    <div v-if="systemsStatus.length" class="status-list">
      <div 
        v-for="service in systemsStatus" 
        :key="service.id" 
        class="service-item"
        :class="service.status"
      >
        <div class="service-info">
          <span class="dot"></span>
          <span class="service-name">{{ service.name }}</span>
        </div>
        <strong class="status-text">{{ formatStatus(service.status) }}</strong>
      </div>
    </div>

    <p v-else class="loading">Sincronizando com os servidores do GitHub...</p>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { useRoute } from 'vue-router'
import axios from 'axios'

const route = useRoute()
const systemsStatus = ref([])
const showWelcomeModal = ref(false)
const timeLeft = ref(30)
let timerInterval = null

// Busca dados da API CORRETA: githubstatus.com
const fetchGlobalStatus = async () => {
  try {
    const response = await axios.get('https://www.githubstatus.com/api/v2/summary.json')
    // Mapeia os componentes conforme exigido no desafio
    systemsStatus.value = response.data.components.slice(0, 7)
  } catch (error) {
    console.error("Falha ao consumir API de Status:", error)
  }
}

// Botão de Atualização Manual (Requisito: Botão para renovar dados)
const refreshData = () => {
  fetchGlobalStatus()
  timeLeft.value = 30 // Reseta o timer ao atualizar manualmente
}

// Atualização Automática (Requisito: Atualizar a cada 30-60 segundos)
const startTimer = () => {
  timerInterval = setInterval(() => {
    if (timeLeft.value > 0) {
      timeLeft.value--
    } else {
      refreshData()
    }
  }, 1000)
}

const formatStatus = (status) => {
  return status.replace('_', ' ').toUpperCase()
}

onMounted(() => {
  fetchGlobalStatus()
  startTimer()

  // Requisito: Detectar query parameter ?popup=welcome
  if (route.query.popup === 'welcome') {
    showWelcomeModal.value = true
  }
})

onUnmounted(() => {
  if (timerInterval) clearInterval(timerInterval)
})
</script>

<style scoped>
.status-container {
  min-height: 100vh;
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
  background: #0f0f0f;
  color: #fff;
  font-family: 'Inter', sans-serif;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 2px solid var(--dynamic-color);
  padding-bottom: 15px;
  margin-bottom: 20px;
}

.header-actions {
  display: flex;
  gap: 10px;
}

.btn-refresh {
  background: transparent;
  border: 1px solid var(--dynamic-color);
  color: var(--dynamic-color);
  border-radius: 5px;
  padding: 5px 10px;
  cursor: pointer;
}

.timer-bar {
  margin-bottom: 20px;
  text-align: right;
}

.progress {
  height: 4px;
  background: var(--dynamic-color);
  box-shadow: 0 0 10px var(--dynamic-color);
  transition: width 1s linear;
  margin-top: 5px;
  border-radius: 2px;
}

.status-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.service-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #1a1a1a;
  padding: 15px;
  border-radius: 10px;
  border: 1px solid rgba(255,255,255,0.1);
  transition: 0.3s;
}

.service-info {
  display: flex;
  align-items: center;
  gap: 10px;
}

.dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: #888;
}

/* Estilização baseada nos status da API oficial */
.operational .dot { background: #42d392; box-shadow: 0 0 8px #42d392; }
.operational .status-text { color: #42d392; }

.degraded_performance .dot, .partial_outage .dot { background: #ff9f43; box-shadow: 0 0 8px #ff9f43; }
.degraded_performance .status-text, .partial_outage .status-text { color: #ff9f43; }

.major_outage .dot { background: #ff5555; box-shadow: 0 0 8px #ff5555; }
.major_outage .status-text { color: #ff5555; }

/* Modal Styles */
.modal-overlay {
  position: fixed;
  top: 0; left: 0; width: 100%; height: 100%;
  background: rgba(0,0,0,0.9);
  display: flex; justify-content: center; align-items: center;
  z-index: 1000;
}

.modal-content {
  background: #1a1a1a;
  padding: 30px;
  border: 2px solid var(--dynamic-color);
  border-radius: 15px;
  text-align: center;
  max-width: 80%;
}

.btn-link {
  margin-top: 20px;
  padding: 10px 20px;
  background: var(--dynamic-color);
  color: #000;
  border: none;
  border-radius: 5px;
  font-weight: bold;
  cursor: pointer;
}

.btn-logout {
  background: #ff5555;
  color: white;
  border: none;
  padding: 5px 15px;
  border-radius: 5px;
  cursor: pointer;
}

.loading {
  color: var(--dynamic-color);
  text-align: center;
  margin-top: 40px;
  font-style: italic;
}
</style>