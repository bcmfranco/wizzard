<template>
    <div class="questions-container">
        <!-- Scale selector -->
        <div class="scale-selector">
            <label for="scale-type">Escala de respuestas:</label>
            <select 
                id="scale-type" 
                v-model="selectedScale" 
                class="scale-select"
            >
                <option value="fibonacci">Fibonacci (1, 2, 3, 5, 8)</option>
                <option value="exponential">Exponencial (1, 2, 4, 8, 16)</option>
                <option value="linear">Lineal (0, 1, 2, 3, 4)</option>
            </select>
        </div>

        <!-- Input area -->
        <div class="input-area">
            <input 
                type="text" 
                v-model="newQuestion"
                placeholder="Escriba una nueva pregunta..."
                @keyup.enter="addQuestion"
                class="question-input"
            >
            <button @click="addQuestion" class="add-button">
                <span>+</span>
            </button>
        </div>

        <!-- Questions list -->
        <div class="questions-list">
            <div 
                v-for="(question, index) in questions" 
                :key="index"
                class="question-item"
            >
                <span class="question-text">{{ question }}</span>
                <button 
                    @click="removeQuestion(index)"
                    class="remove-button"
                >
                    <span>×</span>
                </button>
            </div>
        </div>

        <!-- Save and Navigate buttons -->
        <div class="buttons-area">
            <button @click="saveQuestions" class="save-button" v-if="questions.length > 0">
                Guardar cambios
            </button>
            <NuxtLink to="/forms" class="form-button">
                Ir al formulario
            </NuxtLink>
        </div>

        <p class="info-text">Los cambios serán guardados en el navegador y solamente estarán disponibles acá</p>

        <div v-if="showNotification" class="notification">
            Los cambios fueron guardados.
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const newQuestion = ref('')
const questions = ref([])
const showNotification = ref(false)
const selectedScale = ref('fibonacci')

// Add scales object
const scales = {
    fibonacci: [1, 2, 3, 5, 8],
    exponential: [1, 2, 4, 8, 16],
    linear: [0, 1, 2, 3, 4]
}

onMounted(() => {
    // Load questions and scale from localStorage
    const savedQuestions = localStorage.getItem('wizardQuestions')
    const savedScale = localStorage.getItem('selectedScale')
    
    if (savedQuestions) {
        questions.value = JSON.parse(savedQuestions)
    }
    if (savedScale) {
        selectedScale.value = savedScale
    }
})

const addQuestion = () => {
    if (newQuestion.value.trim()) {
        questions.value.push(newQuestion.value)
        newQuestion.value = ''
    }
}

const removeQuestion = (index) => {
    questions.value.splice(index, 1)
}

const saveQuestions = () => {
    localStorage.setItem('wizardQuestions', JSON.stringify(questions.value))
    localStorage.setItem('selectedScale', selectedScale.value)
    localStorage.setItem('scaleValues', JSON.stringify(scales[selectedScale.value]))
    showNotification.value = true
    setTimeout(() => {
        showNotification.value = false
    }, 3000)
}
</script>

<style scoped>
.questions-container {
    max-width: 800px;
    margin: 2rem auto;
    padding: 2rem;
    font-family: 'Montserrat', sans-serif;
}

.scale-selector {
    margin-bottom: 2rem;
    display: flex;
    align-items: center;
    gap: 1rem;
}

.scale-selector label {
    color: #006647;
    font-size: 1rem;
    font-weight: 500;
}

.scale-select {
    padding: 0.5rem;
    border: 2px solid #006647;
    border-radius: 4px;
    color: #006647;
    background-color: white;
    font-size: 1rem;
    cursor: pointer;
    outline: none;
}

.scale-select:focus {
    border-color: #007857;
    box-shadow: 0 0 0 2px rgba(0, 102, 71, 0.1);
}

.scale-select option {
    padding: 0.5rem;
}

.input-area {
    display: flex;
    gap: 1rem;
    margin-bottom: 2rem;
}

.question-input {
    flex-grow: 1;
    padding: 0.8rem;
    border: 2px solid #006647;
    border-radius: 4px;
    font-size: 1rem;
    color: #006647;
}

.question-input:focus {
    outline: none;
    border-color: #007857;
    box-shadow: 0 0 0 2px rgba(0, 102, 71, 0.1);
}

.add-button {
    background-color: #006647;
    color: white;
    border: none;
    border-radius: 4px;
    width: 40px;
    height: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: all 0.3s ease;
    font-size: 1.5rem;
}

.add-button:hover {
    background-color: #007857;
    transform: scale(1.05);
}

.questions-list {
    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.question-item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 1rem;
    background-color: #f5f5f5;
    border-radius: 4px;
    transition: all 0.3s ease;
}

.question-text {
    color: #666;
    font-size: 1rem;
}

.remove-button {
    background-color: transparent;
    color: #ff4444;
    border: none;
    width: 30px;
    height: 30px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    font-size: 1.5rem;
    transition: all 0.3s ease;
}

.remove-button:hover {
    color: #cc0000;
    transform: scale(1.1);
}

.buttons-area {
    margin-top: 2rem;
    display: flex;
    justify-content: center;
    gap: 1rem;
}

.save-button {
    background-color: #006647;
    color: white;
    border: none;
    border-radius: 4px;
    padding: 0.8rem 2rem;
    font-size: 1rem;
    cursor: pointer;
    transition: all 0.3s ease;
}

.save-button:hover {
    background-color: #007857;
    transform: scale(1.05);
}

.form-button {
    background-color: white;
    color: #006647;
    border: 2px solid #006647;
    border-radius: 4px;
    padding: 0.8rem 2rem;
    font-size: 1rem;
    cursor: pointer;
    transition: all 0.3s ease;
    text-decoration: none;
    display: inline-flex;
    align-items: center;
}

.form-button:hover {
    background-color: #f8f9fa;
    transform: scale(1.05);
}

.notification {
    position: fixed;
    bottom: 2rem;
    right: 2rem;
    background-color: #4CAF50;
    color: white;
    padding: 1rem 2rem;
    border-radius: 4px;
    animation: slideIn 0.3s ease;
    box-shadow: 0 2px 8px rgba(0,0,0,0.2);
}

.info-text {
    text-align: center;
    color: #666;
    font-size: 0.9rem;
    margin-top: 1.5rem;
    font-style: italic;
}

@keyframes slideIn {
    from {
        transform: translateX(100%);
        opacity: 0;
    }
    to {
        transform: translateX(0);
        opacity: 1;
    }
}
</style>