<template>
  <div class="container">
    <!-- <h1>Welcome to Product List</h1> -->

    <!-- For front end search =  searchFunction -->
    <div class="subject" v-for="sub in sortedProducts">
      <p>Subject: {{ sub.subject }}</p>
      <p>Location: {{ sub.location }}</p>
      <p>Price: £{{ sub.price }}</p>
      <p>Spaces: {{ sub.spaces }}</p>
      <img class="img" v-bind:src="sub.image_path" v-bind:alt="sub.image_text" style="height: 100px">
      <button class="addtoCartBtn" id="addtoCartBtn" v-on:click="decrementSpaces(); addToCart(sub); btn(sub);"
       v-if="noMoreSpaces(sub)">
        Add to cart
      </button>
      <button class="addtoCartBtn2 disabled" disabled='disabled' id="addtoCartBtn"
            v-on:click="decrementSpaces(); addToCart(sub); btn(sub);" v-else>
            Add to cart
          </button>
      <!-- <button class="addtoCartBtn" id="addtoCartBtn"
            v-on:click="decrementSpaces(); addToCart(sub); btn(sub); updateSpaces2(sub.spaces, sub._id); getLessonID(sub._id);"
            v-if="noMoreSpaces(sub)">
            Add to cart
          </button> -->
    </div>
  </div>
</template>
<script>
export default {
  name: "Lesson",
  props: ["sortedProducts", "cart", "getID", "getSpaces"],
  data() {
    return {};
  },
  methods: {
    noMoreSpaces: function (sub) {
      console.log(sub.spaces);
      console.log(sub.spaces == 5);
      // check and return if spaces is more than 0, otherwise button will be disabled
      return sub.spaces > 0;
    },
    // Method to decrease number of spaces once user clicks 'Add to Cart' button
    decrementSpaces() {
      if (this.spaces > 0) {
        this.spaces--;
      }
    },
    btn(sub) {
      if (sub.spaces > 0) {
        sub.spaces--;
      }
      console.log(sub.spaces);
    },
    // updateSpaces2(spacesNum, id) {
    //   // Fetch to update lesson spaces with PUT - ID is taken once user clicks on specific lesson
    //   this.getID.push(id);
    //   this.getSpaces.push(spacesNum);
    // },
    addToCart: function (product) {
      // this.cart.push(product); // push the relevant subject to store in the cart array
      // console.log(this.cart);
      // console.log("Added to Arr " + this.cart.length);
      this.$emit("add-item-to-cart", product);
    },
  },
};
</script>
