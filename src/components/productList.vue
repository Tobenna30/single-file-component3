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
          <button 
        @click="addToCart(lesson)" 
        :disabled="!canAddToCart(lesson)">
        Add to Cart
      </button>
      <span v-if="!canAddToCart(lesson)">All out!</span>
      <span v-else-if="itemsLeft(lesson) < 5 && itemsLeft(lesson) > 0">
        Only {{ itemsLeft(lesson) }} left!
      </span>
      <span v-else>Buy now!</span>
  
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: ['lessons', 'cart'],
    computed: {
      filteredLessons() {
        
        if (this.searchTerm) {
          const search = this.searchTerm.toLowerCase();
          return this.lessons.filter(lesson =>
            lesson.subject.toLowerCase().includes(search) ||
            lesson.location.toLowerCase().includes(search));
        }
        return this.lessons; 
      },
    },
    methods: {
      addToCart(lesson) {
        if (lesson.spaces > 0) {
          
          lesson.spaces--;
  
         
          this.$emit('add-item-to-cart', {
            id: lesson.id,
            subject: lesson.subject,
            location: lesson.location,
            price: lesson.price,
            icon: lesson.icon,
          });
        }
      },
      canAddToCart: function (lesson) {
             
             return lesson.spaces > this.cartCount(lesson.id);
           },
           cartCount(id) {
            let count = 0;
            for (let i = 0; i < this.cart.length; i++) {
                if (this.cart[i] === id) {
                    count++;
                }
  
            }
            return count;
        },
    itemsLeft(lesson) {
          
          return lesson.spaces - this.cartCount(lesson.id);
          
        },
  
    }
  }
  </script>
