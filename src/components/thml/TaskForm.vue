<template>
  <div class="task-form-container">
    <h2>➕ Adicionar Nova Tarefa</h2>
    
    <form @submit.prevent="addTask">
      
      <div class="form-group">
        <label for="title">Título da Tarefa:</label>
        <input type="text" id="title" v-model="newTask.title" required>
      </div>

      <div class="form-group">
        <label for="discipline">Disciplina:</label>
        <select id="discipline" v-model="newTask.discipline" required>
          <option value="" disabled>Selecione a disciplina</option>
          <option v-for="d in disciplines" :key="d" :value="d">{{ d }}</option>
        </select>
      </div>
      
      <div class="form-group">
        <label for="type">Tipo de Atividade:</label>
        <select id="type" v-model="newTask.type" required>
          <option value="Entrega">📝 Entrega/Trabalho</option>
          <option value="Prova">🧠 Prova/Teste</option>
          <option value="Leitura">📚 Leitura/Estudo</option>
        </select>
      </div>

      <div class="form-group">
        <label for="dueDate">Data de Vencimento:</label>
        <input type="date" id="dueDate" v-model="newTask.dueDate" required>
      </div>
      
      <div class="form-group">
        <label for="weight">Peso na Média (%):</label>
        <input type="number" id="weight" v-model.number="newTask.weight" min="0" max="100">
      </div>

      <button type="submit" class="submit-button">Salvar Tarefa</button>
      <button type="button" @click="$emit('close')" class="cancel-button">Cancelar</button>
    </form>
  </div>
</template>

<script setup>
import { ref, defineEmits } from 'vue';

// Define o evento que será emitido para o componente pai (Dashboard)
const emit = defineEmits(['taskAdded', 'close']);

// Estado inicial da nova tarefa
const newTask = ref({
  title: '',
  discipline: '',
  type: 'Entrega', // Padrão
  dueDate: '',
  weight: 0,
});

// Mock de Disciplinas (Isto viria de uma API ou Store real)
const disciplines = ref([
    'Cálculo I',
    'História Moderna',
    'Lab. de Física',
    'Projeto Integrador'
]);

// --- Função Principal: Adicionar Tarefa ---
const addTask = () => {
    // 1. Validar e formatar os dados
    if (!newTask.value.title || !newTask.value.discipline || !newTask.value.dueDate) {
        alert('Por favor, preencha todos os campos obrigatórios.');
        return;
    }

    // 2. Criar o objeto final da tarefa
    const taskPayload = {
        id: Date.now(), // ID simples para mock
        ...newTask.value,
        // Adiciona um status inicial para que a Dashboard saiba onde colocá-la
        status: getInitialStatus(newTask.value.dueDate),
    };

    // 3. Emitir o evento para o componente pai (Dashboard)
    emit('taskAdded', taskPayload);

    // 4. Resetar o formulário após a emissão
    newTask.value = {
        title: '',
        discipline: '',
        type: 'Entrega',
        dueDate: '',
        weight: 0,
    };
    
    // Opcional: Fechar o formulário
    emit('close');
};

// --- Lógica de Apoio: Determinar Coluna Inicial (status) ---
const getInitialStatus = (dueDateStr) => {
    const today = new Date();
    const due = new Date(dueDateStr);
    const diffTime = due.getTime() - today.getTime();
    const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
    
    // Lógica para determinar a coluna Kanban:
    if (diffDays <= 7) {
        return 'Entregas Imediatas';
    }
    // Simplificando: todas as outras tarefas vão para Planejamento inicialmente
    return 'Planejamento'; 
};
</script>

<style scoped>
/* Estilos Específicos do Formulário */
.task-form-container {
    background-color: var(--card-bg, #2a2a3e);
    padding: 30px;
    border-radius: 10px;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.5);
    width: 400px;
    margin: 20px auto; /* Centraliza para o exemplo */
}

h2 {
    color: var(--primary-color, #7b68ee);
    border-bottom: 2px solid var(--bg-dark, #1e1e2d);
    padding-bottom: 15px;
    margin-top: 0;
}

.form-group {
    margin-bottom: 20px;
}

label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
    color: var(--text-light, #e0e0e0);
}

input[type="text"],
input[type="date"],
input[type="number"],
select {
    width: 100%;
    padding: 10px;
    border: 1px solid var(--bg-dark, #1e1e2d);
    border-radius: 5px;
    background-color: var(--bg-dark, #1e1e2d);
    color: var(--text-light, #e0e0e0);
    box-sizing: border-box;
}

.submit-button, .cancel-button {
    padding: 10px 15px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    margin-right: 10px;
}

.submit-button {
    background-color: var(--primary-color, #7b68ee);
    color: white;
}

.cancel-button {
    background-color: #555;
    color: white;
}
</style>