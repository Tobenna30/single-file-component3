<!-- <script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
</script> -->



<template>
  <div id="app">
    <!-- <header>
      <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

      <div class="wrapper">
        <HelloWorld msg="You did it!" />
      </div>
    </header> -->

    <header>
      <h2>{{sitename}}</h2>
    <button @click="showCheckout">  {{itemsInTheCart}}
      <font-awesome-icon :icon="['fas', 'fa-shopping-cart']" />
      Cart
    </button>
    
    <button v-if="testConsole" @click="toggleShowTestConsole">
      <font-awesome-icon :icon="['fas', 'fa-text-height']" />
      Test Console
    </button>
    
    <div v-if="testConsole &&  showTestConsole" class="test-console">
      <button @click="deleteAllCaches" class="test-elem">
        <font-awesome-icon :icon="['fas', 'fa-trash']" />
        Delete All Caches
      </button>

      <button @click="saveProductToDB">
  <font-awesome-icon :icon="['fas', 'save']" />
  Test Save a product
</button>

      <button @click="unregisterAllServiceWorkers" class="test-elem">
        <font-awesome-icon :icon="['fab', 'fa-uniregistry']" />
          Unregister All ServiceWorkers
      </button>

      <button @click="reloadPage" class="test-elem">
        <font-awesome-icon :icon="['fas', 'fa-sync']" />
          Reload Page
      </button>

      <strong class="test-elem">HTTPS Test: </strong>
      <a v-bind:href="serverURL" target="_blank">link</a>

    </div>

  </header>

  
    <!-- <main>
      <TheWelcome />
    </main> -->
    <main>
      <component :is="currentView"></component>
    </main>
  </div>
</template>

<script>
import productList from './components/productList.vue'
import checkout from './components/checkout.vue'

export default {
  name: 'lesson-app',
  data() {
    return {
      sitename: 'Individual Demonstration Booking System',
      cart: [],
      currentView: productList,
      testConsole: true,
      showTestConsole: true,
      serverURL: "https://lesseonapp3-env.eba-mwhmmtep.eu-west-2.elasticbeanstalk.com/collections/lessons",
    }
  },
  components: {
    productList,
    checkout
  },
  methods: {
    showCheckout() { // Corrected the method name to match the button's @click directive
      this.currentView = this.currentView === productList ? checkout : productList;
    },
    toggleShowTestConsole() {
        this.showTestConsole = !this.showTestConsole; // This will toggle the value between true and false
      },
      unregisterAllServiceWorkers() {
      navigator.serviceWorker.getRegistrations().then(function (registrations) {
        for (let registration of registrations) {
          registration.unregister();
        }
      });
      console.log("ServiceWorkers Unregistered");
    },
    deleteAllCaches() {
      caches.keys().then(function (names) {
        for (let name of names)
          caches.delete(name);
      });
      console.log("All Caches Deleted");
    },
    reloadPage() {
      window.location.reload();
    },
    addToCart(lesson) {
      if (lesson.spaces > 0) {
        // Decrease the lesson space
        lesson.spaces--;

        // Add the lesson to the cart
        this.cart.push({
          id: lesson.id,
          subject: lesson.subject,
          location: lesson.location,
          price: lesson.price,
          icon: lesson.icon,
        });
        eventBus.$emit('item-added', this.cart);
      }
    },
    saveProductToDB() {
      const newProduct = {
        "id": 1,
        "subject": "Science",
        "location": "Lab B",
        "price": 25,
        "spaces": 5,
        "icon": "fa-flask"
      }

      fetch("https://lesseonapp3-env.eba-mwhmmtep.eu-west-2.elasticbeanstalk.com/collections/lessons", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(newProduct),
      })
        .then((response) => {
          if (response.ok) {
            // Check if the response status is OK (e.g., 200 or 201)
            return response.json(); // Parse the JSON response
          }
        })
        .then((json) => {
          alert("Success: Product saved to the database");
          console.log("Success: Product saved to the database", json);

          // Optionally, you can update your frontend data or UI as needed
          webstore.lessons.push(newProduct);
        })
        .catch((error) => {
          console.error("Error saving product:", error);
          alert("Error: Failed to save product to the database");
        });


    },

  },
  computed: {
      
      itemsInTheCart: function () {
        return this.cart.length || "";
      },
}
};
</script>


<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
