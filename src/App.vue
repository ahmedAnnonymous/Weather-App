<template>
    <div class="app">
      <!-- Header -->
      <WeatherHeader @search-click="showSearch = true" />
  
      <!-- Search Modal -->
      <SearchModal
        v-if="showSearch"
        v-model:show="showSearch"
        @city-selected="selectCity"
      />
  
      <!-- City Tabs -->
      <CityTabs
        :cities="cities"
        :active-tab="activeTab"
        @tab-change="activeTab = $event"
      />
  
      <!-- Weather Content -->
      <WeatherContent
        :loading="loading"
        :error="error"
        :weather-data="weatherData"
        :hourly-data="hourlyData"
        :daily-data="dailyData"
        :last-updated="lastUpdated"
        @refresh="refreshData"
      />
    </div>
  </template>
  
  <script setup>
  import { ref, computed, onMounted, watch } from 'vue'
  import axios from 'axios'
  import WeatherHeader from './components/WeatherHeader.vue'
  import SearchModal from './components/SearchModal.vue'
  import CityTabs from './components/CityTabs.vue'
  import WeatherContent from './components/WeatherContent.vue'
  import { API_KEY, BASE_URL } from './config/api'
  
  // State
  const cities = ref([
    { id: 3451190, name: 'Rio de Janeiro', country: 'BR' },
    { id: 1816670, name: 'Beijing', country: 'CN' },
    { id: 5368361, name: 'Los Angeles', country: 'US' }
  ])
  
  const activeTab = ref(0)
  const loading = ref(false)
  const error = ref(null)
  const weatherData = ref(null)
  const hourlyData = ref([])
  const dailyData = ref([])
  const lastUpdated = ref(null)
  const showSearch = ref(false)
  
  // Computed
  const currentCity = computed(() => cities.value[activeTab.value])
  
  // Methods
  const fetchWeatherData = async () => {
    loading.value = true
    error.value = null
    
    try {
      const city = currentCity.value
      
      // Fetch current weather and forecast
      const [currentResponse, forecastResponse] = await Promise.all([
        axios.get(`${BASE_URL}/weather`, {
          params: {
            id: city.id,
            appid: API_KEY,
            units: 'metric'
          }
        }),
        axios.get(`${BASE_URL}/forecast`, {
          params: {
            id: city.id,
            appid: API_KEY,
            units: 'metric'
          }
        })
      ])
  
      weatherData.value = currentResponse.data
      
      // Process hourly data (next 4 hours)
      hourlyData.value = forecastResponse.data.list.slice(0, 4).map(item => ({
        time: new Date(item.dt * 1000).toLocaleTimeString('en-US', { 
          hour: 'numeric', 
          hour12: true 
        }),
        temp: Math.round(item.main.temp),
        rain: item.pop ? Math.round(item.pop * 100) : 0,
        icon: item.weather[0].icon,
        description: item.weather[0].description
      }))
  
      // Process daily data (next 5 days)
      const dailyMap = new Map()
      forecastResponse.data.list.forEach(item => {
        const date = new Date(item.dt * 1000)
        const dateKey = date.toDateString()
        
        if (!dailyMap.has(dateKey)) {
          dailyMap.set(dateKey, {
            date: date,
            temps: [],
            icon: item.weather[0].icon,
            description: item.weather[0].description
          })
        }
        
        dailyMap.get(dateKey).temps.push(item.main.temp)
      })
  
      dailyData.value = Array.from(dailyMap.values()).slice(0, 5).map((day, index) => ({
        date: day.date,
        dayName: index === 0 ? 'Today' : day.date.toLocaleDateString('en-US', { weekday: 'short' }),
        dateStr: day.date.toLocaleDateString('en-US', { month: 'short', day: 'numeric' }),
        tempMin: Math.round(Math.min(...day.temps)),
        tempMax: Math.round(Math.max(...day.temps)),
        icon: day.icon,
        description: day.description
      }))
  
      lastUpdated.value = new Date().toLocaleString('en-US', {
        month: 'short',
        day: 'numeric',
        hour: 'numeric',
        minute: '2-digit',
        hour12: false
      })
  
    } catch (err) {
      error.value = 'Failed to fetch weather data. Please try again.'
      console.error('Weather API Error:', err)
    } finally {
      loading.value = false
    }
  }
  
  const selectCity = (city) => {
    const newCity = {
      id: city.id,
      name: city.name,
      country: city.country
    }
    
    // Add to cities if not already there
    const existingIndex = cities.value.findIndex(c => c.id === city.id)
    if (existingIndex === -1) {
      cities.value.push(newCity)
      activeTab.value = cities.value.length - 1
    } else {
      activeTab.value = existingIndex
    }
    
    showSearch.value = false
  }
  
  const refreshData = () => {
    fetchWeatherData()
  }
  
  // Lifecycle
  onMounted(() => {
    fetchWeatherData()
  })
  
  // Watch for tab changes
  watch(activeTab, () => {
    fetchWeatherData()
  })
  </script>