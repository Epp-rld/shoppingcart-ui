<template>
  <div class="container mt-4">
    <h1 class="text-center mb-4">Shopping Cart</h1>

    <form @submit.prevent="addProduct" class="row mb-3">
      <input v-model="newProduct.name" placeholder="Product" class="form-control col" required />
      <input v-model.number="newProduct.quantity" type="number" placeholder="Qty" class="form-control col" required />
      <input v-model.number="newProduct.price" type="number" placeholder="Price" class="form-control col" required />
      <button class="btn btn-outline-secondary col">Add</button>
    </form>
    <table class="table">
      <thead>
      <tr>
        <th>Product</th>
        <th>Quantity</th>
        <th>Price</th>
        <th>Action</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="item in cartItems" :key="item">
        <td>{{ item.name }}</td>
        <td>{{ item.quantity }}</td>
        <td>{{ item.price }}€</td>
        <td>
          <button @click="removeProduct(item.name)" class="btn btn-danger btn-sm rainbow-button">Remove</button>
        </td>
      </tr>
      </tbody>

    </table>

    <h3 class="mt-4">Total: {{ cartTotal.toFixed(2) }}€</h3>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: () => ({
    api: "http://localhost:8089/api/cart",
    newProduct: {name: "", price: 0, quantity: 1},
    cartItems: [],
    cartTotal: 0,
  }),
  methods: {
    fetchCart() {
      axios.all([
        axios.get(`${this.api}/items`).then(res => (this.cartItems = res.data)),
        axios.get(`${this.api}/total`).then(res => (this.cartTotal = res.data)),
      ]);
    },
    addProduct() {
      axios.post(`${this.api}/add`, this.newProduct).then(this.fetchCart);
      this.newProduct = {name: "", price: 0, quantity: 1};
    },
    removeProduct(name) {
      axios.delete(`${this.api}/remove/${name}`).then(this.fetchCart);
    },
  },
  mounted() {
    this.fetchCart();
  },
};
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  text-align: center;
}

.product-table {
  width: 100%;
  border-collapse: collapse;
  margin: 20px 0;
}

.product-table th,
.product-table td {
  border: 1px solid #ddd;
  padding: 10px;
  text-align: center;
}

.product-table th {
  background-color: #f4f4f4;
  font-weight: bold;
}

.action-button {
  padding: 5px 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.action-button:hover {
  background-color: #0056b3;
}

/* Rainbow Button Style */
@keyframes rainbow {
  0% { background-color: red; }
  16.7% { background-color: orange; }
  33.3% { background-color: yellow; }
  50% { background-color: green; }
  66.7% { background-color: blue; }
  83.3% { background-color: indigo; }
  100% { background-color: violet; }
}

.rainbow-button {
  background-size: 200% 200%;
  animation: rainbow 3s infinite;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: transform 0.2s;
}

.rainbow-button:hover {
  transform: scale(1.05);
}
</style>


