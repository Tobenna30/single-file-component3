<template>
  <div>
    <div>Welcome to the product list</div>
    <div>
      <div v-for="lesson in filteredLessons" :key="lesson.id" class="lesson">
        <font-awesome-icon :icon="['fas', lesson.icon]" />
        <p>Subject: {{ lesson.subject }}</p>
        <p>Location: {{ lesson.location }}</p>
        <p>Price: ${{ lesson.price }}</p>
        <p>Spaces: {{ lesson.spaces }}</p>
        <button @click="addToCart(lesson)" :disabled="lesson.spaces === 0">Add to Cart</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['lessons'],
  computed: {
    filteredLessons() {
      // Check if searchTerm is not empty
      if (this.searchTerm) {
        const search = this.searchTerm.toLowerCase();
        return this.lessons.filter(lesson =>
          lesson.subject.toLowerCase().includes(search) ||
          lesson.location.toLowerCase().includes(search));
      }
      return this.lessons; // Return all lessons if searchTerm is empty
    },
  },
  methods: {
    addToCart(lesson) {
      if (lesson.spaces > 0) {
        // Decrease the lesson space
        lesson.spaces--;

        // Emit an event to the parent component with the item details
        this.$emit('add-item-to-cart', {
          id: lesson.id,
          subject: lesson.subject,
          location: lesson.location,
          price: lesson.price,
          icon: lesson.icon,
        });
      }
    }
  }
}
</script>
