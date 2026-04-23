<template>
  <div class="login-container">
    <h2>🔐 LOGIN</h2>
    
    <form @submit.prevent="handleLogin">
      <div class="field">
        <label>E-mail:</label>
        <input v-model="email" type="email" placeholder="teste@teste.com" required />
      </div>

      <div class="field">
        <label>Senha:</label>
        <input v-model="password" type="password" placeholder="******" required />
      </div>

      <button type="submit">Entrar</button>
    </form>
    
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
  </div>
</template>



<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router' // Importamos o "motorista" das rotas

// Variáveis reativas
const email = ref('')
const password = ref('')
const errorMessage = ref('')

const router = useRouter() // Inicializamos o roteador

const handleLogin = () => {
  // Limpa erros anteriores
  errorMessage.value = ''

  // 1. Validação básica de regras
  if (password.value.length < 6) {
    errorMessage.value = 'A senha deve ter pelo menos 6 caracteres!'
    return
  }

  // 2. Simulação de autenticação
  if (email.value === 'teste@teste.com' && password.value === '123456') {
    // Se estiver correto, "empurramos" o usuário para a rota /dashboard
    router.push('/dashboard')
  } else {
    errorMessage.value = 'E-mail ou senha incorretos!'
  }
}
</script>



<style scoped>
/* Fundo e Container principal */
.login-container {
  max-width: 350px;
  margin: 80px auto;
  padding: 30px;
  background-color: #1a1a1a; /* Preto profundo */
  border: 2px solid #42d392;   /* Borda verde Vue */
  border-radius: 15px;
  box-shadow: 0 0 20px rgba(66, 211, 146, 0.2); /* Brilho neon suave */
  text-align: center;
}

h2 {
  color: #bd93f9; /* Roxo clássico (estilo Dracula/Neon) */
  margin-bottom: 25px;
  font-weight: 800;
  text-transform: uppercase;
  letter-spacing: 2px;
}

.field {
  display: flex;
  flex-direction: column;
  text-align: left;
  margin-bottom: 20px;
}

label {
  color: #42d392; /* Verde Vue para os rótulos */
  font-weight: bold;
  margin-bottom: 8px;
  font-size: 0.9em;
}

/* Inputs Estilo Neon */
input {
  background-color: #242424;
  border: 1px solid #42d392;
  border-radius: 8px;
  padding: 12px;
  color: #bd93f9; /* Letras em Roxo ao digitar */
  outline: none;
  transition: 0.3s;
}

input:focus {
  border-color: #647eff; /* Azul Vue no foco */
  box-shadow: 0 0 10px rgba(100, 126, 255, 0.5);
}

/* Placeholder roxo clarinho */
input::placeholder {
  color: rgba(189, 147, 249, 0.5);
}

/* Botão de Ação */
button {
  width: 100%;
  padding: 15px;
  background: linear-gradient(135deg, #42d392, #647eff); /* Degradê Vue */
  color: #1a1a1a;
  border: none;
  border-radius: 8px;
  font-weight: 900;
  text-transform: uppercase;
  cursor: pointer;
  transition: transform 0.2s;
}

button:hover {
  transform: scale(1.02);
  box-shadow: 0 0 15px rgba(66, 211, 146, 0.6);
}

.error {
  color: #ff5555;
  margin-top: 15px;
  font-weight: bold;
  animation: shake 0.2s ease-in-out 0s 2; /* Pequeno tremor ao aparecer */
}

@keyframes shake {
  0% { margin-left: 0rem; }
  25% { margin-left: 0.5rem; }
  75% { margin-left: -0.5rem; }
  100% { margin-left: 0rem; }
}
</style>