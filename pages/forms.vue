<template>
    <div class="wizard-container">
        <div 
            v-for="question in questions" 
            :key="question.id"
            class="card" 
            :class="{ 
                active: currentCard === question.id,
                'summary-card': question.type === 'summary'
            }"
        >
            <!-- Regular card content -->
            <template v-if="!question.type">
                <p class="question-content">{{ question.content }}</p>
                <h2 class="question-title">{{ question.title }}</h2>
                
                <div class="answers-container">
                    <div 
                        v-for="(answer, index) in question.answers" 
                        :key="index"
                        class="answer-option"
                    >
                        <input 
                            type="radio" 
                            :id="`answer-${question.id}-${index}`"
                            :name="`question-${question.id}`"
                            :value="answer"
                            v-model="selectedAnswers[question.id]"
                        >
                        <label :for="`answer-${question.id}-${index}`">{{ answer }}</label>
                    </div>
                </div>

                <div class="button-container">
                    <button 
                        v-if="question.showPrevious"
                        @click="previousCard"
                    >
                        Volver
                    </button>
                    <button 
                        v-if="question.showNext"
                        @click="nextCard"
                    >
                        Siguiente
                    </button>
                </div>
            </template>

            <!-- Summary card content -->
            <template v-else-if="question.type === 'summary'">
                <h2 class="summary-title">{{ question.title }}</h2>
                <p class="summary-content">{{ question.content }}</p>
                <div class="summary-answers">
                    <div class="total-sum">
                        <strong>Story Points:</strong>
                        <span>{{ calculateTotalSum() }}</span>
                    </div>
                </div>
            </template>
        </div>
    </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { wizardQuestions } from '../data/questions'

const currentCard = ref(1)
const questions = ref(wizardQuestions)
const selectedAnswers = reactive({})

const nextCard = () => {
    if (currentCard.value < questions.value.length) {
        currentCard.value++
    }
}

const previousCard = () => {
    if (currentCard.value > 1) {
        currentCard.value--
    }
}

const calculateTotalSum = () => {
    return Object.values(selectedAnswers)
        .reduce((sum, answer) => sum + Number(answer), 0)
}
</script>

<style scoped>
.wizard-container {
    max-width: 600px;
    margin: 2rem auto;
    position: relative;
}

.card {
    background: white;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    display: none;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.card.active {
    display: block;
}

.button-container {
    display: flex;
    justify-content: space-between;
    margin-top: 2rem;
}

button {
    padding: 0.5rem 1rem;
    background-color: #4CAF50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s;
}

button:hover {
    background-color: #45a049;
}

.question-content {
    color: #333;
    margin-bottom: 1rem;
    font-size: 1rem;
    font-weight: 600;
}

.question-title {
    color: #666;
    line-height: 1.5;
    font-size: 1rem;
    font-weight: normal;
    margin-bottom: 1.5rem;
}

.answers-container {
    margin: 1.5rem 0;
    display: grid;
    grid-template-rows: 1fr;
    grid-template-columns: repeat(3, 1fr);
    gap: 1rem;
}

.answer-option {
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0.5rem;
    border: 1px solid #ddd;
    border-radius: 4px;
    transition: all 0.3s ease;
}

.answer-option:hover {
    background-color: #f5f5f5;
}

.answer-option input[type="radio"] {
    margin-right: 0.5rem;
}

.answer-option label {
    cursor: pointer;
    font-size: 1rem;
    color: #444;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

.answer-option input[type="radio"]:checked + label {
    color: #4CAF50;
    font-weight: 600;
}

.answer-option input[type="radio"]:checked ~ .answer-option {
    border-color: #4CAF50;
    background-color: #f0fff0;
}

.summary-card {
    background-color: #f9f9f9;
    border: 1px solid #ddd;
}

.summary-title {
    font-size: 1rem;
    font-weight: 700;
    margin-bottom: 1rem;
    color: #2c3e50;
}

.summary-content {
    font-size: 1rem;
    margin-bottom: 1.5rem;
    color: #34495e;
}

.summary-answers {
    margin-top: 1rem;
    padding: 1rem;
    background: #e8f5e9;
    border-radius: 4px;
    border: 2px solid #4CAF50;
}

.summary-answer {
    margin-bottom: 0.5rem;
    font-size: 1rem;
    color: #333;
}

.total-sum {
    margin-top: 2rem;
    padding: 1rem;
    background: #e8f5e9;
    border-radius: 4px;
    border: 2px solid #4CAF50;
}

.total-sum strong {
    display: block;
    margin-bottom: 0.5rem;
    color: #2c3e50;
    font-size: 2rem;
}

.total-sum span {
    font-size: 1rem;
    color: #4CAF50;
    font-weight: bold;
    font-size: 3rem;
}

.button-container button:first-child {
    background-color: white;
    color: #4CAF50;
    border: 1px solid #4CAF50;
    box-shadow: 0 1px 3px rgba(0,0,0,0.1);
}

.button-container button:first-child:hover {
    background-color: #f8f9fa;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.button-container button:last-child {
    background-color: #4CAF50;
    color: white;
    border: none;
    box-shadow: 0 2px 4px rgba(0,0,0,0.2);
}

.button-container button:last-child:hover {
    background-color: #45a049;
    box-shadow: 0 3px 6px rgba(0,0,0,0.2);
}
</style>