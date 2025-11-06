<template>
    <div>
        <Header />
        <div class="wizard-container">
            <NuxtLink to="/questions" class="settings-button">
                ⚙️
            </NuxtLink>

            <div 
                v-for="question in questions" 
                :key="question.id"
                class="card" 
                :class="{ 
                    active: currentCard === question.id,
                    'answered': isQuestionAnswered(question.id),
                    'welcome-card': question.type === 'welcome'
                }"
            >
                <!-- Welcome card content -->
                <template v-if="question.type === 'welcome'">
                    <p class="question-content">{{ question.content }}</p>
                    <div class="button-container">
                        <button 
                            @click="currentCard++"
                            class="start-button"
                        >
                            Comenzar
                        </button>
                    </div>
                </template>

                <!-- Regular card content -->
                <template v-else-if="!question.type">
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
                    <div class="button-container">
                        <button 
                            class="refresh-button"
                            @click="refreshForm"
                        >
                            Realizar nuevamente
                        </button>
                    </div>
                </template>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue'
import Header from '~/components/Header.vue'

const currentCard = ref(0) // Changed to start at 0
const questions = ref([])
const selectedAnswers = reactive({})

onMounted(() => {
    const savedQuestions = localStorage.getItem('wizardQuestions')
    const scaleValues = localStorage.getItem('scaleValues')
    const formHeader = localStorage.getItem('formHeader')
    
    if (savedQuestions && scaleValues && formHeader) {
        // Create initial welcome card
        const welcomeCard = {
            id: 0,
            title: "",
            content: formHeader,
            showPrevious: false,
            showNext: false, // We'll use a special button instead
            type: 'welcome'
        }

        // Transform saved questions into the wizard format
        const loadedQuestions = JSON.parse(savedQuestions).map((question, index) => ({
            id: index + 1,
            title: "Complejidad",
            content: question,
            showPrevious: true,
            showNext: true,
            answers: JSON.parse(scaleValues)
        }))

        // Add the summary card
        const summaryCard = {
            id: loadedQuestions.length + 1,
            type: 'summary',
            title: "Resumen de Story Points",
            content: "Total de puntos acumulados",
            showPrevious: false,
            showNext: false,
            answers: []
        }

        // Combine all cards
        questions.value = [welcomeCard, ...loadedQuestions, summaryCard]
    }
})

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

const handleAnswerSelect = (question) => {
    // Aseguramos que la respuesta se guarde antes de avanzar
    setTimeout(() => {
        if (selectedAnswers[question.id] && question.showNext) {
            // Verificamos que estemos en la tarjeta correcta
            if (currentCard.value === question.id) {
                nextCard()
            }
        }
    }, 200)
}

// Función para verificar si una pregunta fue respondida
const isQuestionAnswered = (questionId) => {
    return !!selectedAnswers[questionId]
}

const calculateTotalSum = () => {
    return Object.values(selectedAnswers)
        .reduce((sum, answer) => sum + Number(answer), 0)
}

const refreshForm = () => {
    // Reset all form data
    currentCard.value = 1
    Object.keys(selectedAnswers).forEach(key => delete selectedAnswers[key])
}
</script>

<style scoped>
.wizard-container {
    max-width: 600px;
    margin: 2rem auto;
    position: relative;
    background: inherit; /* Updated lighter green background */
    padding: 2rem;
    border-radius: 12px;
    min-height: 400px;
}

.card {
    background: white;
    color: #006647;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    display: none;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    border: 1px solid #006647;
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
    color: #00845c;
    border: 1px solid #00845c;
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
    color: #006647;
    margin: 0px;
    font-size: 22px;
    font-weight: 600;
}

.question-title {
    color: #006647;
    line-height: 14px;
    font-size: 14px;
    font-weight: normal;
    margin: 10px 0px;
}

.answers-container {
    margin: 1.5rem 0;
    display: grid;
    grid-template-rows: 1fr;
    grid-template-columns: repeat(5 , 1fr);
    gap: 1rem;
}

.answer-option {
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0.5rem;
    border: 1px solid #006647;
    border-radius: 4px;
    transition: all 0.3s ease;
    cursor: pointer;
    user-select: none;
    background-color: #006647;
    min-height: 3rem;
    transform: scale(1);
    transform-origin: center;
}

.answer-option:hover {
    background-color: #007857;
    transform: scale(1.05);
    z-index: 1;
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

.answer-option span {
    color: white;
    font-size: 1rem;
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

.card.answered .answer-option[data-selected="true"] {
    background-color: #7ac554;
    border-color: white;
}

.summary-card {
    background: white;
    border: 1px solid #006647;
}

.summary-title {
    color: #006647;
}

.summary-content {
    color: #006647;
}

.summary-answers {
    background: rgba(255, 255, 255, 0.1);
    border: 2px solid white;
}

.summary-answer {
    color: white;
}

.total-sum {
    background: white;
    border: 2px solid #006647;
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
    color: #006647;
    font-size: 0.9rem;
    text-transform: uppercase;
    letter-spacing: 1px;
    margin-bottom: 1rem;
}

.total-sum span {
    color: #006647;
    font-size: 3.5rem;
    font-weight: bold;
    line-height: 1;
}

.progress-bar {
    width: 100%;
    height: 6px;
    background-color: inherit;
    border-radius: 3px;
    margin-bottom: 2rem;
    overflow: hidden;
}

.progress-fill {
    height: 100%;
    background-color: white;
    transition: width 0.3s ease;
}

.refresh-button {
    background-color: #006647 !important;
    color: white !important;
    border: none !important;
    padding: 0.8rem 1.5rem !important;
    font-size: 1rem;
    margin-top: 2rem;
}

.refresh-button:hover {
    background-color: #007857 !important;
    box-shadow: 0 4px 8px rgba(0,0,0,0.2) !important;
}

.settings-button {
    position: fixed;
    bottom: 2rem;
    right: 2rem;
    background: white;
    color: #006647;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.8rem;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    border: 2px solid #006647;
    transition: all 0.3s ease;
    text-decoration: none;
    z-index: 1000;
}

.settings-button:hover {
    transform: rotate(45deg);
    background: #006647;
    color: white;
    box-shadow: 0 4px 12px rgba(0,0,0,0.2);
}

.welcome-card {
    text-align: center;
}

.start-button {
    background-color: #006647;
    color: white;
    border: none;
    border-radius: 4px;
    padding: 1rem 2rem;
    font-size: 1.2rem;
    cursor: pointer;
    transition: all 0.3s ease;
    margin-top: 2rem;
}

.start-button:hover {
    background-color: #007857;
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}
</style>