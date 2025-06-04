<template>
    <div class="weather-content">
      <div v-if="loading" class="loading">
        <div class="spinner"></div>
        <p style="margin-top: 20px;">Loading weather data...</p>
      </div>
  
      <div v-else-if="error" class="error">
        <p>{{ error }}</p>
        <button class="btn-primary" @click="$emit('refresh')">
          Retry
        </button>
      </div>
  
      <div v-else-if="weatherData">
        <div class="refresh-hint">
          <a @click="$emit('refresh')">Tap to refresh ↻</a>
        </div>
  
        <!-- Next Hours Forecast -->
        <HourlyForecast :hourly-data="hourlyData" />
  
        <!-- Next 5 Days Forecast -->
        <DailyForecast :daily-data="dailyData" />
      </div>
  
      <!-- Last Updated -->
      <div v-if="lastUpdated && !loading" class="last-updated">
        Last updated on {{ lastUpdated }}
      </div>
    </div>
  </template>
  
  <script setup>
  import HourlyForecast from './HourlyForecast.vue'
  import DailyForecast from './DailyForecast.vue'
  
  defineProps({
    loading: Boolean,
    error: String,
    weatherData: Object,
    hourlyData: Array,
    dailyData: Array,
    lastUpdated: String
  })
  
  defineEmits(['refresh'])
  </script>
  
  <style scoped>
  .weather-content {
    padding: 20px;
    min-height: calc(100vh - 200px);
  }
  
  .refresh-hint {
    text-align: right;
    margin-bottom: 10px;
    font-size: 12px;
    color: #666;
  }
  
  .refresh-hint a {
    color: #1565C0;
    text-decoration: none;
    cursor: pointer;
  }
  
  .refresh-hint a:hover {
    text-decoration: underline;
  }
  
  .last-updated {
    text-align: center;
    padding: 15px;
    font-size: 12px;
    color: #666;
    background-color: #f5f5f5;
    margin: -20px -20px 0;
  }
  
  .error {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
  }
  </style>