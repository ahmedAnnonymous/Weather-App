<template>
    <div class="tabs">
      <button 
        v-for="(city, index) in cities" 
        :key="city.id"
        :class="['tab', { active: activeTab === index }]"
        @click="$emit('tab-change', index)"
      >
        {{ city.name }}
      </button>
    </div>
  </template>
  
  <script setup>
  defineProps({
    cities: {
      type: Array,
      required: true
    },
    activeTab: {
      type: Number,
      required: true
    }
  })
  
  defineEmits(['tab-change'])
  </script>
  
  <style scoped>
  .tabs {
    display: flex;
    background-color: #f5f5f5;
    border-bottom: 1px solid #ddd;
    overflow-x: auto;
  }
  
  .tab {
    flex: 1;
    min-width: 120px;
    padding: 15px;
    text-align: center;
    cursor: pointer;
    background: none;
    border: none;
    font-size: 14px;
    font-weight: 500;
    color: #666;
    position: relative;
    transition: all 0.3s;
    text-transform: uppercase;
    white-space: nowrap;
  }
  
  .tab.active {
    color: #1565C0;
  }
  
  .tab.active::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 3px;
    background-color: #FF6B35;
  }
  
  .tab:hover {
    background-color: #eeeeee;
  }
  </style>