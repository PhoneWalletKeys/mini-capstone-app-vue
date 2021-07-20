<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <div v-for="product in products" v-bind:key="product.id">
      <h2>Product: {{ product.title }}</h2>
      <p>Name: {{ product.name }}</p>
      <p>Price: {{ product.price }}</p>
      <img v-bind:src="product.image_url" :alt="product.name" />
      <p><button v-on:click="showProduct(product)">More Info!</button></p>
    </div>
    <button v-on:click="createProduct">Create Product</button>
    <dialog id="product-details">
      <form method="dialog">
        <h1>Product Info!</h1>
        <p>
          Name:
          <input type="text" v-model="currentProduct.name" />
        </p>
        <p>
          Description:
          <input type="text" v-model="currentProduct.description" />
        </p>
        <p>
          Image_url:
          <input type="text" v-model="currentProduct.image_url" />
        </p>
        <p>
          Price:
          <input type="text" v-model="currentProduct.price" />
        </p>
        <button v-on:click="updateProduct(currentProduct)">Update Product</button>
        <button v-on:click="deleteProduct(currentProduct)">Delete Product</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>
<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Hello World!",
      products: [],
      newProductParams: {},
      currentProduct: {},
    };
  },
  created: function () {
    this.indexProducts();
  },
  methods: {
    indexProducts: function () {
      axios.get("http://localhost:3000/products").then((response) => {this.products = response.data;
        console.log("All Products:", this.products);
      });
    },
    createProduct: function () {
      console.log("created a product");
      var params = {
        name: "more product",
        description: "some more info",
        price: 2,
        image_url: "",
      };
      axios
        .post("http://localhost:3000/products/", params)
        .then((response) => this.products.push(response.data))

        .catch((error) => console.log(error.response));
    },
    showProduct: function (product) {
      console.log(product);
      this.currentProduct = product;
      document.querySelector("#product-details").showModal();
    },
    updateProduct: function (product) {
      var editProductParams = product;
      axios.patch("http://localhost:3000/products/" + product.id, editProductParams).then((response) => {
        console.log("Success!", response.data);
      });
    },
    deleteProduct: function (product) {
      axios.delete("http://localhost:3000/products/" + product.id).then((response) => {
        console.log("Deleted", response.data);
        var index = this.products.indexOf(response);
        this.products.splice(index, 1);
      });
    },
  },
};
</script>
