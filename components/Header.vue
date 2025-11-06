<template>
    <header class="header">
        <NuxtLink to="/" class="logo-container">
            <div class="logo-mask"></div>
            <h1 class="brand-name">Flowquest</h1>
        </NuxtLink>
    </header>
</template>

<style scoped>
.header {
    height: 70px;
    background-color: rgba(252, 252, 252, 0.2);
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    display: grid;
    justify-items: center;
}

.logo-container {
    display: flex;
    align-items: center;
    gap: 1rem;
    text-decoration: none;
    cursor: pointer;
}

.logo-mask {
    height: 40px;
    width: 40px;
    background-color: white;
    mask: url('@/assets/logo.svg') no-repeat center;
    -webkit-mask: url('@/assets/logo.svg') no-repeat center;
    mask-size: contain;
    -webkit-mask-size: contain;
}

.brand-name {
    color: white;
    font-size: 1.5rem;
    font-weight: 600;
    margin: 0;
    font-size: 42px;
}
</style>