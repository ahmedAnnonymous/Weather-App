<template>
    <div class="weather-section">
      <h2 class="section-title">Next hours</h2>
      <div class="hourly-forecast">
        <div v-for="hour in hourlyData" :key="hour.time" class="hour-item">
          <div class="temp">{{ hour.temp }}°</div>
          <div class="rain">{{ hour.rain }}%</div>
          <div class="icon">
            <img 
              :src="`https://openweathermap.org/img/wn/${hour.icon}@2x.png`"
              :alt="hour.description"
              class="weather-icon"
            >
          </div>
          <div class="time">{{ hour.time }}</div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  defineProps({
    hourlyData: {
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
    margin-bottom: 20px;
  }
  
  .section-title {
    font-size: 20px;
    font-weight: 500;
    margin-bottom: 20px;
    color: #333;
  }
  
  .hourly-forecast {
    display: flex;
    justify-content: space-between;
    gap: 10px;
  }
  
  .hour-item {
    flex: 1;
    text-align: center;
  }
  
  .hour-item .temp {
    font-size: 24px;
    font-weight: 500;
    color: #333;
    margin-bottom: 5px;
  }
  
  .hour-item .rain {
    font-size: 14px;
    color: #42A5F5;
    margin-bottom: 10px;
  }
  
  .hour-item .icon {
    width: 50px;
    height: 50px;
    margin: 0 auto 10px;
  }
  
  .hour-item .time {
    font-size: 14px;
    color: #666;
  }
  </style>