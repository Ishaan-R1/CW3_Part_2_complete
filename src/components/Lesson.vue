<template>
  <div>
    <div class="order">
        <p>Order:<br></p>
        <form>
            <input type="radio" id="a" name="fav_language" value="Ascending" v-on:click="ascend()">
            <label for="html">Ascending</label><br>
            <input type="radio" id="d" name="fav_language" value="Descending" v-on:click="descend()">
            <label for="css">Descending</label><br>
        </form>
    </div>
    <form>
      <div class="container2">
        <div class="sort">
          <p>Sort By:<br></p>
          <div v-if="this.sortOrder.order === 'ascend'">
            <input type="radio" id="subject" name="fav_language" value="Subject" v-model="radioBtn"
                v-on:click="sort('subject')">
                  <label for="subject">Subject</label><br>
                <input type="radio" id="subject" name="fav_language" value="Location" v-model="radioBtn"
                v-on:click="sort('location')">
                <label for="subject">Location</label><br>
                <input type="radio" id="subject" name="fav_language" value="Price" v-model="radioBtn"
                v-on:click="sort('price')">
                <label for="subject">Price</label><br>
                <input type="radio" id="subject" name="fav_language" value="Availability" v-model="radioBtn"
                v-on:click="sort('spaces')">
                <label for="subject">Availability</label><br>
          </div>
          <div v-else>
            <input type="radio" id="subject" name="fav_language" value="Subject" v-model="radioBtn"
                v-on:click="sort2('subject')">
                <label for="subject">Subject</label><br>
              <input type="radio" id="subject" name="fav_language" value="Location" v-model="radioBtn"
                v-on:click="sort2('location')">
                <label for="subject">Location</label><br>
                <input type="radio" id="subject" name="fav_language" value="Price" v-model="radioBtn"
                v-on:click="sort2('price')">
                <label for="subject">Price</label><br>
                <input type="radio" id="subject" name="fav_language" value="Availability" v-model="radioBtn"
                v-on:click="sort2('spaces')">
                <label for="subject">Availability</label><br>
          </div>
        </div>
      </div>

    </form>

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
    </div>
  </div>
</div>
</template>
<script>
export default {
  name: "Lesson",
  props: ["sortedProducts", "cart", "getID", "getSpaces", "sortOrder", "ascend", "descend"],
  data() {
    return {
      radioBtn: {
        option: "",
      },
    };
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
    addToCart: function (product) {
      this.$emit("add-item-to-cart", product);
    },
    sort: function (option) {
      this.$emit("sort-item", option);
    },
    sort2: function (option) {
      this.$emit("sort-item2", option);
    },
  },
};
</script>
