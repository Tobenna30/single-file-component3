<template>

    <div>

        <div>
            Welcome to the product list
        </div>

<!-- 
    <input type="text" id="searchTerm" placeholder="Enter search term" v-model="searchQuery">
    <button @click="performSearch">Search</button>

    <h2>Search Results</h2>
    <ul id="searchResults"></ul> -->


    
    <div>
    <div v-for="lesson in filteredLessons" :key="lesson.id" class="lesson">
        <font-awesome-icon :icon="['fas', lesson.icon]" />

      <p>Subject: {{ lesson.subject }}</p>
      <p>Location: {{ lesson.location }}</p>
      <p>Price: ${{ lesson.price }}</p>
      <p>Spaces: {{ lesson.spaces }}</p>
      <button @click="$emit('add-item-to-cart', lesson)" :disabled="lesson.spaces === 0">Add to Cart</button>
    </div>
  </div>

    </div>
</template>

<script>
export default {
  props: ['lessons'], // Ensure this is receiving the full lessons list
  computed: {
    filteredLessons() {
    // Check if searchTerm is not empty
    if (this.searchTerm) {
      const search = this.searchTerm.toLowerCase();
      return this.lessons.filter(lesson => lesson.subject.toLowerCase().includes(search) ||
        lesson.location.toLowerCase().includes(search));
    }
    return this.lessons; // Return all lessons if searchTerm is empty
  },
  }
}
</script>
