<template>
  <div id="app">
    <h1>{{ sitename }}</h1>
    <button @click="showCheckout()" v-if="canViewCart" id="CartBtn">
      <!-- Displaying cart icon on button -->
      <img src="Images/cart.png" alt="Cart" class="cart" />
      {{ cartItemCount }}
      Checkout
    </button>
    <!-- Creating disabled button when no items are added to cart -->
    <button class="disabled" id="CartBtnDisabled" disabled='disabled' v-else>
        <img src="Images/cart.png" alt="Cart" class="cart2">
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
        @sort-item="sort"
        @sort-item2="sort2"
        @manage-remove-lesson="manageRemoveLesson"
        :order="order"
        :checkForm="checkForm"
        :orderMsg="orderMsg"
        :sortOrder="sortOrder"
        :ascend="ascend"
        :descend="descend"
      ></component>
    </main>
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
      sortOrder: {
      order: "ascend",
      },
      radioBtn: {
        option: "",
      },
      // Object to hold data of message when order is successful
      orderMsg: {
        msg: "Thank for your order. Your order has been placed.",
      },
      // Object to hold data of user inputted order details
      order: {
        firstName: "",
        lastName: "",
        address: "",
        phone: "",
      },
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
    sort: function (option) {
      // create function to compare each element
      function compare(a, b) {
        if (a[option] > b[option]) return 1;
        if (a[option] < b[option]) return -1;
        return 0;
      }
      this.subjects.sort(compare); // sort all the already compared elements
    },
    sort2: function (option) {
      // create function to compare each element
      function compare(a, b) {
        if (a[option] > b[option]) return -1;
        if (a[option] < b[option]) return 1;
        return 0;
      }
      // sort all the already compared elements
      this.subjects.sort(compare);
    },
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
    ascend: function () {
      this.sortOrder.order = "ascend";
      console.log(this.sortOrder.order);
    },
    descend: function () {
      this.sortOrder.order = "descend";
      console.log(this.sortOrder.order);
    },
  },
  computed: {
    // Computed property to check if checkout button can be enabled or not
    canViewCart: function () {
      return this.cartItemCount >= 1; // check if number of items in cart array is more than or equal to 1
    },
    // Computed property to check how many items are added to cart array
    cartItemCount: function () {
      // Displays number of items in cart array or returns empty string if array is empty
      return this.cart.length || "";
    },
    // Method to sort subjects in ascended order
    sortedProducts() {
      return this.subjects;
    },
    oneItemInCart: function () {
      return this.cartItemCount >= 1;
    },
    // Computed property to check if checkout  button on checkout page can be enabled or not
    checkForm: function () {
      return (
        // Regular expressions to check if the first and last name contains only letters
        /^[a-zA-Z]+$/.test(this.order.firstName) &&
        /^[a-zA-Z]+$/.test(this.order.lastName) &&
        // Regular expressions to check if the phone number contains only numbers
        /^[0-9]+$/.test(this.order.phone) &&
        // Check if all fields are inputted
        this.order.firstName.length > 0 &&
        this.order.lastName.length > 0 &&
        this.order.address.length > 0 &&
        this.order.phone.length > 0
      );
    },
  },
};
</script>
