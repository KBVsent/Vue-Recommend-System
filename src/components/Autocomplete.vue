<template>
  <div v-if="suggestions.length && searchText" class="autocomplete" v-bind:style="{ zIndex: 9999 }">
    <ul>
      <li v-for="(suggestion, index) in suggestions" :key="index" @click="selectSuggestion(suggestion)">
        {{ suggestion.text }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    suggestions: {
      type: Array,
      required: true,
    },
    searchText: {
      type: String,
      required: true,
    },
  },
  methods: {
    selectSuggestion(suggestion) {
      this.$emit('suggestion-selected', suggestion.id)
    },
  },
}
</script>

<style scoped>
.autocomplete {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  max-height: 200px;
  overflow-y: auto;
  transition: all 0.2s ease-in-out;
}

.autocomplete-enter,
.autocomplete-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

li {
  padding: 8px 12px;
  cursor: pointer;
  font-family: 'Roboto', sans-serif;
  color: #333;
  transition: all 0.2s ease-in-out;
}

li:hover {
  background-color: #f5f5f5;
  transform: scale(1.02);
}

@media (max-width: 768px) {
  .autocomplete {
    width: 90%;
  }
}
</style>