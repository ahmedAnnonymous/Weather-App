<template>
    <header class="header">
      <h1>Simple Weather</h1>
      <button class="search-btn" @click="$emit('search-click')">🔍</button>
    </header>
  </template>
  
  <script setup>
  defineEmits(['search-click'])
  </script>
  
  <style scoped>
  .header {
    background-color: #1565C0;
    color: white;
    padding: 15px 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: relative;
  }
  
  .header h1 {
    font-size: 20px;
    font-weight: 400;
  }
  
  .search-btn {
    background: none;
    border: none;
    color: white;
    font-size: 24px;
    cursor: pointer;
    padding: 5px;
  }
  
  .search-btn:hover {
    opacity: 0.8;
  }
  </style>