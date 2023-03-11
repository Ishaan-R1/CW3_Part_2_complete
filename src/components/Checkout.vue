<template>
  <div>
    <h1>Welcome to Checkout List</h1>
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
  </div>
</template>
<script>
export default {
  name: "Checkout",
  props: ["sortedProducts", "cart", "getID", "getSpaces"],
  data() {
    return {};
  },
  methods: {
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
