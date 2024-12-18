<template>
  <div>
    <div style="text-align: center;">
      <h1 style="color: white; background: linear-gradient(45deg, #43CBFF, #9708CC); padding: 5px; display: inline-block; border-radius: 10px; font-size: 1em;">
        <img src="/src/assets/img/logo.png" alt="Logo" style="border-radius: 10px; max-width: 100%; height: auto;">
      </h1>
    </div>

    <div class="input-wrapper">
      <div class="input-container">
        <input v-model="animeId" type="text" placeholder="输入动漫名称" class="styled-input" @input="filterSuggestions" />
        <br>
        <br>
        <Autocomplete :suggestions="suggestions" :search-text="animeId" @suggestion-selected="handleSuggestionSelected" />
      </div>
    </div>

    <div style="text-align: center;">
      <h1 style="color: white;">📺 推荐列表：</h1>
    </div>

    <div class="card-container">
      <div v-for="anime in recommendations" class="card" @click="getRecommendations(anime.id)">
        <img :src="anime.img" alt="Anime image" class="card-image" />
        <div class="card-content">
          <h2><a :href="`https://bgm.tv/subject/${anime.id}`" target="_blank">{{ anime.name }}</a></h2>
          <h3>{{ anime.name_cn }}</h3>
          <p>{{ truncateSummary(anime.summary) }}</p>
          <p><a :href="`https://bgm.tv/subject/${anime.id}/stats`" target="_blank">评分:</a> {{ anime.score }}</p>
          <div class="tags">
            <span v-for="(tag, index) in anime.tags" :key="index" :style="getTagStyle(tag)" class="tag" v-show="index < 5 || showAllTags">{{ tag }}</span>
            <span v-if="anime.tags.length > 5 && !showAllTags" class="more-tags" @click.stop="toggleAllTags">更多...</span>
            <span v-if="anime.tags.length > 5 && showAllTags" class="more-tags" @click.stop="toggleAllTags">收起</span>
          </div>
        </div>
      </div>
    </div>

    <p class="copyright">&copy; 2024&nbsp; CopyRight Vsent</p>
  </div>
</template>

<script>
import axios from 'axios'
import Autocomplete from './components/Autocomplete.vue'
import suggestionsData from './assets/search.json'

export default {
  components: {
    Autocomplete,
  },
  data() {
  return {
    animeId: '',
    recommendations: [],
    suggestions: [],
    showAllTags: false, // 添加这一行
    }
  },
  mounted() {
    this.suggestions = suggestionsData
    this.getRandomRecommendations() // 在组件挂载时获取随机推荐列表
  },
  methods: {
    toggleAllTags() {
      this.showAllTags = !this.showAllTags
    },
    filterSuggestions() {
      if (this.animeId.trim() === '') {
        this.suggestions = []
      } else {
        this.suggestions = suggestionsData.filter(suggestion => suggestion.text.toLowerCase().includes(this.animeId.toLowerCase()))
      }
    },
    handleSuggestionSelected(id) {
      this.animeId = ''
      this.getRecommendations(id)
    },
    getRecommendations(id) {
      axios
        .get('http://127.0.0.1:5000/recommend/' + id)
        .then(response => {
          this.recommendations = response.data
        })
        .catch(error => {
          console.log(error)
        })
    },
    getRandomRecommendations() {
      axios
        .get('http://127.0.0.1:5000/random_recommend')
        .then(response => {
          this.recommendations = response.data
        })
        .catch(error => {
          console.log(error)
        })
    },
    truncateSummary(summary) {
      if (summary.length > 100) {
        return summary.slice(0, 100) + '...'
      }
      return summary
    },
    getTagStyle(tag) {
      const colors = ['#FF7F00', '#00FF00', '#0000FF', '#9400D3']
      const randomColor = colors[Math.floor(Math.random() * colors.length)]
      const width = tag.length * 0.8 + 2 + 'em'
      return `background-color: ${randomColor}; width: ${width};`
    }
  },
}
</script>

<style scoped>
.styled-input {
  padding: 10px 15px;
  font-size: 16px;
  border-radius: 25px;
  border: none;
  background-color: #f5f5f5;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
  width: 300px;
}

.styled-input:focus {
  outline: none;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  transform: scale(1.05);
}

.styled-input::placeholder {
  color: #999;
  transition: color 0.3s ease;
}

.styled-input:focus::placeholder {
  color: transparent;
}

.input-wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-bottom: 5vmin;
}

.input-container {
  position: relative;
}

.card-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 20px;
}

.card {
  width: 300px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  cursor: pointer;
  transition: transform 0.3s ease;
}

.card:hover {
  transform: translateY(-5px);
}

.card-image {
  width: 100%;
  height: 400px;
  object-fit: cover;
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
}

.card-content {
  padding: 20px;
}

.card-content h2 {
  font-size: 20px;
  margin-bottom: 10px;
}

.card-content h3 {
  font-size: 16px;
  color: #888;
  margin-bottom: 10px;
}

.card-content p {
  font-size: 14px;
  color: #555;
  margin-bottom: 10px;
}

.tag {
  display: inline-block;
  padding: 4px 10px;
  margin: 4px;
  border-radius: 20px;
  color: white;
  font-size: 14px;
  text-align: center;
  white-space: nowrap;
}
.more-tags {
  display: inline-block;
  padding: 4px 10px;
  margin: 4px;
  border-radius: 20px;
  background-color: #ccc;
  color: #333;
  font-size: 14px;
  text-align: center;
  white-space: nowrap;
  cursor: pointer;
}
</style>