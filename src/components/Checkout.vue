<template>
  <div>
    <h1>Welcome to the Shopping Cart</h1>
    <h2>Current Items in the Cart {{ cartItemCount }}</h2>
    <div v-for="(product, index) in cart">
      <div  class="subject2">
          <p>Subject: {{ product.subject }}</p>
          <p>Location: {{ product.location }}</p>
          <p>Price: £{{ product.price }}</p>
          <p>Spaces: {{ product.spaces }}</p>

          <img class="img" v-bind:src="product.image_path" v-bind:alt="product.image_text" style="height: 100px">
          <!-- Button to call function to remove specifc element from the cart array -->
          <button class="addtoCartBtn"
            v-on:click="manageRemoveLesson(product, index); addBackRemovedLesson(product);">Remove</button>
        </div>
    </div>
    <div class="divider"></div>
    <p>Fill in all fields</p>
      <p>First name and last name must include letters only</p>
      <p>Phone number and last name must include numbers only</p><br>
      <!-- Creating form to display labels and input fields for full name, address and phone number  -->
      <p>
        <strong class="labels">First Name:</strong>
        <!-- Hold user input of first name in 'order' object -->
        <input class="field" v-model.trim="order.firstName" name="name" />
      </p>
      <p id="fnameFeedback"></p>
      <p>
        <strong class="labels">Last Name:</strong>
        <!-- Hold user input of last name in 'order' object -->
        <input class="field" v-model.trim="order.lastName" />
      </p>
      <!-- Hold user input of address in 'order' object -->
      <p><strong class="labels">Address:</strong> <input class="field" v-model.trim="order.address" /></p>
      <!-- Hold user input of phone number in 'order' object -->
      <p><strong class="labels">Phone:</strong> <input class="field" v-model.trim="order.phone" name="phone_number" />
      </p>
      <!-- Display all the user inputted informtation to the user for confirmation of correct details -->
      <h1>Order Information</h1>
      <p>First Name: {{order.firstName}}</p>
      <p>Last Name: {{order.lastName}}</p>
      <p>Address: {{order.address}}</p>
      <p>Phone: {{order.phone}}</p>

      <button class="cb" v-on:click="checkoutBtn();"
        v-if="checkForm">Checkout</button>
      <!-- Disable checkout button if fields not filled correctly -->
      <button class="cb disabled" disabled='disabled' v-else>
        Checkout
      </button>
      <br><br>
      <!-- </form> -->
      <div class="divider"></div>
      <!-- para to display confirmation message once user checks out correctly -->
      <p id="confirmCheckout"></p>
  </div>
</template>
<script>
import { onRenderTracked } from 'vue';

export default {
  name: "Checkout",
  props: ["sortedProducts", "cart", "getID", "getSpaces", "order", "checkForm", "orderMsg"],
  data() {
    return {};
  },
  methods: {

    // Method to create confirmation button
    checkoutBtn: function () {
      let para = document.createElement("p"); // Createe p tag in index.html
      para.innerText = this.orderMsg.msg; // add order message in this paragraph and append to id
      document.getElementById("confirmCheckout").appendChild(para);
    },
    oneInCart(subject) {
      return this.cart.includes(subject.id)
    },
    manageRemoveLesson(subject) {
      this.$emit("manage-remove-lesson", subject);
    },
    addBackRemovedLesson(sub) {
      sub.spaces++; // Display new current spaces number to user
    },
  },
  computed: {
    // Computed property to check how many items are added to cart array
    cartItemCount: function () {
      // Displays number of items in cart array or returns empty string if array is empty
      return this.cart.length || "";
    },
    
  },
};
</script>
