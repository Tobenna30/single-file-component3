

<template>
  <div id="app">
    

    <header>
      <h2>{{sitename}}</h2>
      <button @click="showCheckout">Cart ({{ itemsInTheCart }})
  <font-awesome-icon :icon="['fas', 'fa-shopping-cart']"></font-awesome-icon>
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

  
  

    <main>
      <component :is="currentView" 
  :lessons="lessons" 
  :cart="cart"
  @add-item-to-cart="addToCart"
  @remove-item-from-cart="removeLessonFromCart">
</component>


    </main>
  </div>
</template>

<script>
import productList from './components/productList.vue'
import checkout from './components/checkout.vue';

// import lessons from './assets/json/lessons.json'


export default {
  name: 'lesson-app',
  data() {
    return {
      sitename: 'Individual Demonstration Booking System',
      cart: [],
      currentView: productList,
      //  lessons: lessons ,
      lessons: [],
      serverURL: "https://lesseonapp3-env.eba-mwhmmtep.eu-west-2.elasticbeanstalk.com/collections/lessons",

      testConsole: true,
      showTestConsole: true,
      searchTerm: '',
      
      
    }
  },
  components: {
    productList,
    checkout
  },

  created: function () {
    // fetch("http://localhost:3000/collections/lessons").then(
    //   function (response) {
    //     response.text().then(
    //       function (text) {
    //         alert(text);
    //       }
    //     )
    //   }
    // )

    if ("serviceWorker" in navigator) {
      navigator.serviceWorker.register("service-worker.js");
   }

  let webstore = this;
    //  fetch that retrieves all the lessons with GET
    // fetch("http://localhost:3000/collections/lessons").then(
    fetch("https://lesseonapp3-env.eba-mwhmmtep.eu-west-2.elasticbeanstalk.com/collections/lessons").then(  
      function (response) {
        response.json().then(
          function (json) {
            // alert(json);
            // console.log(json);
            webstore.lessons = json;
          }
        )
      }
    )
  },

  methods: {
    
    showCheckout() { 
      this.currentView = this.currentView === productList ? checkout : productList;
    },
    toggleShowTestConsole() {
        this.showTestConsole = !this.showTestConsole; 
      },
      toggleShowProduct(){
          this.showProduct = this.showProduct ? false : true;
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
  console.log('Before adding, cart length:', this.cart.length);
  try {
   
    lesson.spaces--;
    this.cart.push({
      id: lesson.id,
      subject: lesson.subject,
      location: lesson.location,
      price: lesson.price,
      icon: lesson.icon,
    });
    console.log('After adding, cart length:', this.cart.length);
  } catch (error) {
    console.error('Error adding to cart:', error);
  }
},
removeLessonFromCart(itemId) {
    const index = this.cart.findIndex(item => item.id === itemId);
    if (index !== -1) {
      
      const lesson = this.lessons.find(lesson => lesson.id === this.cart[index].id);
      if (lesson) lesson.spaces++;
      
      
      this.cart.splice(index, 1);
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
      
    itemsInTheCart() {
      return this.cart.length;
    },

      sortedLessons() {
  return this.lessons.slice().sort((a, b) => {
    if (
      (this.sortBy === 'subject' || this.sortBy === 'location') &&
      a[this.sortBy] &&
      b[this.sortBy]
    ) {
      const nameA = a[this.sortBy].toUpperCase();
      const nameB = b[this.sortBy].toUpperCase();
      if (this.sortOrder === 'asc') {
        return nameA.localeCompare(nameB);
      } else {
        return nameB.localeCompare(nameA);
      }
    } else if (a[this.sortBy] && !b[this.sortBy]) {
      return -1;
    } else if (!a[this.sortBy] && b[this.sortBy]) {
      return 1;
    } else {
      return 0;
    }
  });
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
