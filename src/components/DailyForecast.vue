<template>
    <div class="weather-section">
      <h2 class="section-title">Next 5 days</h2>
      <div class="daily-forecast">
        <div v-for="day in dailyData" :key="day.date" class="daily-item">
          <div class="icon">
            <img 
              :src="`https://openweathermap.org/img/wn/${day.icon}@2x.png`"
              :alt="day.description"
              class="weather-icon"
            >
          </div>
          <div class="day-info">
            <div class="day-name">{{ day.dayName }}, {{ day.dateStr }}</div>
            <div class="description">{{ day.description }}</div>
          </div>
          <div class="temps">
            {{ day.tempMax }}° {{ day.tempMin }}°
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  defineProps({
    dailyData: {
      type: Array,
      required: true
    }
  })
  </script>
  
  <style scoped>
  .weather-section {
    background-color: #f5f5f5;
    border-radius: 8px;
    padding: 20px;
  }
  
  .section-title {
    font-size: 20px;
    font-weight: 500;
    margin-bottom: 20px;
    color: #333;
  }
  
  .daily-item {
    display: flex;
    align-items: center;
    padding: 15px 0;
    border-bottom: 1px solid #e0e0e0;
  }
  
  .daily-item:last-child {
    border-bottom: none;
  }
  
  .daily-item .icon {
    width: 40px;
    height: 40px;
    margin-right: 15px;
  }
  
  .daily-item .day-info {
    flex: 1;
  }
  
  .daily-item .day-name {
    font-size: 16px;
    font-weight: 500;
    color: #333;
    margin-bottom: 5px;
  }
  
  .daily-item .description {
    font-size: 14px;
    color: #666;
    text-transform: capitalize;
  }
  
  .daily-item .temps {
    font-size: 16px;
    color: #333;
    font-weight: 500;
  }
  </style>