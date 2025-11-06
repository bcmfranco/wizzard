<template>
    <div>
        <Header />
        <div class="home-container">
            <div class="content">
                <h1 class="title">Flowquest</h1>
                <p class="description">Estima tus historias de usuario de manera ágil y efectiva</p>

                <div class="buttons">
                    <NuxtLink to="/forms" class="action-button primary">
                        Comenzar estimación
                    </NuxtLink>
                    <NuxtLink to="/questions" class="action-button secondary">
                        Configurar preguntas
                    </NuxtLink>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import Header from '~/components/Header.vue'
</script>

<style scoped>
.home-container {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #8bdc65;
    padding: 2rem;
}

.content {
    text-align: center;
    color: white;
}

.title {
    font-size: 3rem;
    margin-bottom: 1rem;
    font-weight: bold;
}

.description {
    font-size: 1.2rem;
    margin-bottom: 3rem;
    opacity: 0.9;
}

.buttons {
    display: flex;
    gap: 1rem;
    justify-content: center;
}

.action-button {
    padding: 1rem 2rem;
    border-radius: 8px;
    text-decoration: none;
    font-weight: 500;
    transition: all 0.3s ease;
}

.action-button.primary {
    background-color: white;
    color: #8bdc65;
}

.action-button.secondary {
    background-color: white;
    color: #8bdc65;
}

.action-button:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}
</style>
