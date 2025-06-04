<template>
    <div class="search-modal" @click.self="closeModal">
      <div class="search-container">
        <div class="close-search">
          <button class="close-btn" @click="closeModal">×</button>
        </div>
        <input 
          v-model="searchQuery"
          type="text"
          class="search-input"
          placeholder="Search for a city..."
          @keyup.enter="searchCities"
        >
        <div v-if="searchLoading" class="loading">
          <div class="spinner"></div>
        </div>
        <div v-else-if="searchResults.length > 0" class="search-results">
          <div 
            v-for="city in searchResults" 
            :key="city.id || city.name"
            class="search-result-item"
            @click="selectCity(city)"
          >
            <span>{{ city.displayName || `${city.name}, ${city.country}` }}</span>
            <span v-if="city.temp !== null" class="search-result-weather">
              {{ city.temp }}°C {{ city.weather }}
            </span>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, watch } from 'vue'
  import axios from 'axios'
  import { API_KEY, BASE_URL } from '../config/api'
  
  const props = defineProps({
    show: Boolean
  })
  
  const emit = defineEmits(['update:show', 'city-selected'])
  
  const searchQuery = ref('')
  const searchResults = ref([])
  const searchLoading = ref(false)
  
  const closeModal = () => {
    searchQuery.value = ''
    searchResults.value = []
    emit('update:show', false)
  }
  
  const searchCities = async () => {
    if (!searchQuery.value.trim()) {
      searchResults.value = []
      return
    }
  
    searchLoading.value = true
    const query = searchQuery.value.trim()
    
    try {
      const response = await axios.get(`${BASE_URL}/find`, {
        params: {
          q: query,
          appid: API_KEY,
          cnt: 50,
          type: 'like',
          sort: 'population',
          lang: 'en'
        }
      })
  
      const cities = response.data.list
      const uniqueCities = []
      const seen = new Set()
      
      cities.forEach(city => {
        const key = `${city.name}-${city.sys.country}`
        if (!seen.has(key)) {
          seen.add(key)
          uniqueCities.push({
            id: city.id,
            name: city.name,
            country: city.sys.country,
            lat: city.coord.lat,
            lon: city.coord.lon,
            temp: city.main ? Math.round(city.main.temp - 273.15) : null,
            weather: city.weather ? city.weather[0].description : null,
            displayName: `${city.name}, ${city.sys.country}`
          })
        }
      })
  
      uniqueCities.sort((a, b) => {
        const aExact = a.name.toLowerCase() === query.toLowerCase()
        const bExact = b.name.toLowerCase() === query.toLowerCase()
        if (aExact && !bExact) return -1
        if (!aExact && bExact) return 1
        return 0
      })
  
      searchResults.value = uniqueCities.slice(0, 20)
    } catch (err) {
      console.error('Search error:', err)
      try {
        const response = await axios.get(`${BASE_URL}/weather`, {
          params: {
            q: query,
            appid: API_KEY
          }
        })
        
        searchResults.value = [{
          id: response.data.id,
          name: response.data.name,
          country: response.data.sys.country,
          displayName: `${response.data.name}, ${response.data.sys.country}`
        }]
      } catch (fallbackErr) {
        searchResults.value = []
      }
    } finally {
      searchLoading.value = false
    }
  }
  
  const selectCity = (city) => {
    emit('city-selected', city)
    closeModal()
  }
  
  // Watch for search query changes with debounce
  let searchTimeout
  watch(searchQuery, (newVal) => {
    clearTimeout(searchTimeout)
    if (newVal.trim()) {
      searchTimeout = setTimeout(() => {
        searchCities()
      }, 500)
    } else {
      searchResults.value = []
    }
  })
  </script>
  
  <style scoped>
  .search-modal {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0,0,0,0.5);
    display: flex;
    align-items: flex-start;
    justify-content: center;
    padding-top: 100px;
    z-index: 1000;
  }
  
  .search-container {
    background: white;
    padding: 20px;
    border-radius: 8px;
    width: 90%;
    max-width: 400px;
  }
  
  .search-input {
    width: 100%;
    padding: 12px;
    font-size: 16px;
    border: 1px solid #ddd;
    border-radius: 4px;
    margin-bottom: 10px;
  }
  
  .search-results {
    max-height: 300px;
    overflow-y: auto;
  }
  
  .search-result-item {
    padding: 12px;
    cursor: pointer;
    border-bottom: 1px solid #eee;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  .search-result-item:hover {
    background-color: #f5f5f5;
  }
  
  .search-result-weather {
    font-size: 12px;
    color: #666;
    margin-left: 10px;
  }
  
  .close-search {
    text-align: right;
    margin-bottom: 10px;
  }
  
  .close-btn {
    background: none;
    border: none;
    font-size: 24px;
    cursor: pointer;
    color: #666;
  }
  </style>