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
                    <label 
                        v-for="(answer, index) in question.answers" 
                        :key="index"
                        class="answer-option"
                        :for="`answer-${question.id}-${index}`"
                        @click="handleAnswerSelect(question)"
                    >
                        <input 
                            type="radio" 
                            :id="`answer-${question.id}-${index}`"
                            :name="`question-${question.id}`"
                            :value="answer"
                            v-model="selectedAnswers[question.id]"
                        >
                        <span>{{ answer }}</span>
                    </label>
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
                        Saltear
                    </button>
                </div>
            </template>

            <!-- Summary card content -->
            <template v-else-if="question.type === 'summary'">
                <h2 class="summary-title">{{ question.title }}</h2>
                    <div class="total-sum">
                        <strong>Story Points:</strong>
                        <span>{{ calculateTotalSum() }}</span>
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

const handleAnswerSelect = (question) => {
    // Small delay to ensure the radio button value is updated
    setTimeout(() => {
        if (question.showNext) {
            nextCard()
        }
    }, 200)
}
</script>

<style scoped>
.wizard-container {
    max-width: 600px;
    margin: 2rem auto;
    position: relative;
}

.card {
    background: #00986b;
    color: white;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    display: none;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    border: 1px solid white;
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
    background-color: white;
    color: #4CAF50;
    border: 1px solid #4CAF50;
    box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    padding: 0.5rem 1rem;
    border-radius: 4px;
    cursor: pointer;
    transition: all 0.3s ease;
}

button:hover {
    background-color: #f8f9fa;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.question-content {
    color: white;
    margin: 0px;
    font-size: 22px;
    font-weight: 600;
}

.question-title {
    color: white;
    line-height: 14px;
    font-size: 14px;
    font-weight: normal;
    margin: 10px 0px;
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
    padding: 1rem;  /* Increased padding to accommodate larger text */
    border: 1px solid white;
    border-radius: 4px;
    transition: all 0.3s ease;
    cursor: pointer;
    user-select: none;
    background-color: #8bdc65; /* Botón de respuesta */
}

.answer-option:hover {
    background-color: #9de379;
}

.answer-option span {
    font-size: 1rem;
    color: white;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

.answer-option input[type="radio"]:checked + span {
    color: white;
    font-weight: 600;
}

.answer-option input[type="radio"]:checked ~ .answer-option {
    border-color: white;
    background-color: #7ac554;
}

.summary-card {
    background: #00986b;
    border: 1px solid white;
}

.summary-title {
    color: white;
}

.summary-content {
    color: white;
}

.summary-answers {
    background: rgba(255, 255, 255, 0.1);
    border: 2px solid white;
}

.summary-answer {
    color: white;
}

.total-sum {
    background: rgba(255, 255, 255, 0.1);
    border: 2px solid white;
    border-radius: 8px;
    padding: 1.5rem;
    aspect-ratio: 4/3;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin: 2rem auto;
    max-width: 300px;
}

.total-sum strong {
    color: white;
    font-size: 0.9rem;
    text-transform: uppercase;
    letter-spacing: 1px;
    margin-bottom: 1rem;
}

.total-sum span {
    color: white;
    font-size: 3.5rem;
    font-weight: bold;
    line-height: 1;
}
</style>