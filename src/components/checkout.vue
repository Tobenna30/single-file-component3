<template>
    <div>
      <h1>Welcome to Checkout</h1>
      <h2>Your cart contains {{ totalItemsInTheCart }} item(s) in total:</h2>
      <div v-for="cartItem in cart" :key="cartItem.id" class="cart-item">
        <font-awesome-icon :icon="['fas', cartItem.icon]" />
        <div v-if="atLeastOneInTheCart(cartItem)" class="product">
          <h3>{{ cartItem.subject }}</h3>
          <p>Lesson ID: {{ cartItem.id }}</p>
          <p>Price: <font-awesome-icon icon="fas fa-pound-sign" /> {{ cartItem.price }}</p>
          <p>Available items: {{ itemsLeft(cartItem) }}
          <strong>In the cart you have added:</strong> {{ cartCount(cartItem.id) }}</p>
          <button @click="removeFromCart(cartItem)">Remove from Cart</button>
        </div>
      </div>
      <input v-model="name" placeholder="Name">
      <input v-model="phone" placeholder="Phone Number">
      <button @click="checkout" :disabled="!validInput">Checkout</button>
      <p v-if="orderSubmitted">Order has been submitted!</p>
    </div>
  </template>
  
  <script>
  export default {
    props: ['lessons', 'cart'],
    data() {
      return {
        name: '',
        phone: '',
        orderSubmitted: false,
      };
    },
    computed: {
      validInput() {
        return this.name.trim().length > 0 && this.phone.trim().length > 0;
      },
      totalItemsInTheCart() {
        return this.cart.length;
      },
    },
    methods: {
      atLeastOneInTheCart(cartItem) {
        return this.cartCount(cartItem.id) > 0;
      },
      cartCount(id) {
    let count = 0;
    for (let i = 0; i < this.cart.length; i++) {
      // Ensure you're comparing the id of the cart item to the passed id
      if (this.cart[i].id === id) {
        count++;
      }
    }
    return count;
  },
  
  itemsLeft(cartItem) {
    return this.cart.length -
this.cartCount(cartItem.id);
},
      removeFromCart(cartItem) {
        this.$emit('remove-item', cartItem.id);
      },
      checkout() {
        if (this.validInput) {
          this.$emit('checkout');
          this.orderSubmitted = true;
          // Reset form fields if necessary
          this.name = '';
          this.phone = '';
          // Add additional logic as necessary
        }
      },
    },
  }
  </script>
  