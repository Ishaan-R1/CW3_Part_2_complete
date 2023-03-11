<!-- <script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
</script> -->

<template>
  <div id="app">
    <!-- <link rel="stylesheet" href="style.css" /> -->
    <!-- <header>
      <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

      <div class="wrapper">
        <HelloWorld msg="You did it!" />
      </div>
    </header> -->
    <!-- <header>
      <h1>{{ sitename }}</h1>

      <button @click="showCheckout()">{{ this.cart.length }} Checkout</button>
    </header> -->
    <h1>{{ sitename }}</h1>
    <button @click="showCheckout()" id="CartBtn">
      <!-- Displaying cart icon on button -->
      <img src="Images/cart.png" alt="Cart" class="cart" />
      {{ cartItemCount }}
      Checkout
    </button>
    <button
      class="testConsoleBtn"
      v-if="testConsole"
      @click="toggleTestConsole"
    >
      Test Console
    </button>
    <div class="testConsoleBdr" v-if="showTestConsole">
      <button @click="reloadPage" class="testConsole">Reload Page</button>
      <button @click="unregisterAllServiceWorkers" class="testConsole">
        Unregister All ServiceWorkers
      </button>
      <button @click="deleteAllCaches" class="testConsole">
        Delete All Caches
      </button>
      <strong class="testConsole">HTTPS Test: </strong>
      <a v-bind:href="awsServerURL" target="_blank">link</a>
    </div>

    <main>
      <component
        :is="currentView"
        :sortedProducts="sortedProducts"
        :cart="cart"
        @add-item-to-cart="addToCart"
        @manage-remove-lesson="manageRemoveLesson"
      ></component>
    </main>
    <!-- <main>
      <TheWelcome />
    </main> -->
  </div>
</template>
<script>
import Lesson from "./components/Lesson.vue";
import Checkout from "./components/Checkout.vue";

import subjects from "./assets/json/lessons_2.json";

export default {
  name: "app",
  data() {
    return {
      sitename: "After School App",
      cart: [],
      currentView: Lesson,
      testConsole: true,
      showTestConsole: false,
      serverURL: "http://localhost:3000/collections/lessons",
      awsServerURL:
        "https://afterschoolapp2-env.eba-wwaj2wgs.eu-west-2.elasticbeanstalk.com/collections/lessons",
      subjects: [],
      getID: [],
      getSpaces: [],
      //subjects: subjects,
    };
  },
  components: { Lesson, Checkout },
  created: function () {
    if ("serviceWorker" in navigator) {
      navigator.serviceWorker.register("service-worker.js");
    }
    // Fetch to retrieve lessons with GET
    let webstore = this;
    fetch(this.awsServerURL).then(function (response) {
      response.json().then(function (json) {
        console.log(json);
        // Push JSON data to subjects array when page is loaded
        webstore.subjects = json;
      });
    });

    // setInterval(this.searchLessons, 1000);
  },
  methods: {
    postOrder(firstName, phone_number, id) {
      const order = {
        name: firstName,
        phone_number: phone_number,
        // Lesson IDs and spaces retrieved from array
        lesson_id: this.orderID.toString(),
        spaces: this.cart.length,
      };
      // Fetch to save new order with POST when submitted
      fetch("https://afterschoolapp2-env.eba-wwaj2wgs.eu-west-2.elasticbeanstalk.com/collections/orders", {
        method: "POST",
        body: JSON.stringify(order),
        headers: {
          "Content-type": "application/json",
        },
      })
        // Convert data to JSON
        .then((response) => response.json())
        .then((json) => console.log(json));
    },
    removeLesson(product) {
      this.cart.splice(this.cart.indexOf(product), 1); // remove specific item in cart array
      console.log(this.cart.indexOf(product));
    },
    manageRemoveLesson(lesson) {
      this.removeLesson(lesson);
      if (!this.oneItemInCart >= 1) {
        this.currentView = Lesson;
      }
    },
    showCheckout() {
      if (this.currentView === Lesson) {
        this.currentView = Checkout;
      } else {
        this.currentView = Lesson;
      }
    },
    displayCheckout() {
      if (this.showProduct) {
        this.showProduct = false;
      } else {
        this.showProduct = true;
      }
    },
    toggleTestConsole() {
      this.showTestConsole = !this.showTestConsole;
    },
    reloadPage() {
      window.location.reload();
    },
    deleteAllCaches() {
      caches.keys().then(function (names) {
        for (let name of names) caches.delete(name);
      });
      console.log("All Caches Deleted");
    },
    unregisterAllServiceWorkers() {
      navigator.serviceWorker.getRegistrations().then(function (registrations) {
        for (let registration of registrations) {
          registration.unregister();
        }
      });
      console.log("ServiceWorkers Unregistered");
    },
    addToCart: function (product) {
      this.cart.push(product); // push the relevant subject to store in the cart array
      console.log(this.cart);
      console.log("Added to Arr " + this.cart.length);
    },
  },
  computed: {
    // Computed property to check how many items are added to cart array
    cartItemCount: function () {
      // Displays number of items in cart array or returns empty string if array is empty
      return this.cart.length || "";
    },
    sortedProducts() {
      function compare(a, b) {
        if (a.price > b.price) return +1;
        if (a.price < b.price) return -1;
        return 0;
      }
      return this.subjects.sort(compare);
    },
    oneItemInCart: function () {
      return this.cartItemCount >= 1;
    },
  },
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
