<template>
    <div class="questions-container">
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
    </div>
</template>

<script setup>
import { ref } from 'vue'

const newQuestion = ref('')
const questions = ref([])

const addQuestion = () => {
    if (newQuestion.value.trim()) {
        questions.value.push(newQuestion.value)
        newQuestion.value = ''
    }
}

const removeQuestion = (index) => {
    questions.value.splice(index, 1)
}
</script>

<style scoped>
.questions-container {
    max-width: 800px;
    margin: 2rem auto;
    padding: 2rem;
    font-family: 'Montserrat', sans-serif;
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
</style>