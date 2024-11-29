<template>
  <div class="container mt-5 p-4 shadow rounded bg-light">
    <h1 class="text-center mb-4 text-primary">Shopping Cart</h1>

    <form @submit.prevent="addProduct" class="row g-3 align-items-center mb-4">
      <div class="col-md-4">
        <input
            v-model="newProduct.name"
            type="text"
            placeholder="Product"
            class="form-control"
            required
        />
      </div>
      <div class="col-md-2">
        <input
            v-model.number="newProduct.quantity"
            type="number"
            placeholder="Qty"
            class="form-control"
            min="1"
            required
        />
      </div>
      <div class="col-md-2">
        <input
            v-model.number="newProduct.price"
            type="number"
            placeholder="Price"
            class="form-control"
            step="0.01"
            min="0"
            required
        />
      </div>
      <div class="col-md-4 text-end">
        <button class="btn btn-outline-primary w-100">Add Product</button>
      </div>
    </form>

    <table class="table table-striped table-hover align-middle">
      <thead class="table-primary">
      <tr>
        <th>Product</th>
        <th>Quantity</th>
        <th>Price (€)</th>
        <th>Action</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="item in cartItems" :key="item.name">
        <td>{{ item.name }}</td>
        <td>{{ item.quantity }}</td>
        <td>{{ item.price }}</td>
        <td>
          <button
              @click="removeProduct(item.name)"
              class="btn btn-danger btn-sm rainbow-button"
          >
            Remove
          </button>
        </td>
      </tr>
      </tbody>
    </table>

    <h3 class="mt-4 text-end text-primary">Total: {{ cartTotal.toFixed(2) }}€</h3>
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
      axios
          .all([
            axios.get(`${this.api}/items`).then((res) => (this.cartItems = res.data)),
            axios.get(`${this.api}/total`).then((res) => (this.cartTotal = res.data)),
          ])
          .catch((error) => console.error(error));
    },
    addProduct() {
      axios
          .post(`${this.api}/add`, this.newProduct)
          .then(this.fetchCart)
          .catch((error) => console.error(error));
      this.newProduct = {name: "", price: 0, quantity: 1};
    },
    removeProduct(name) {
      axios
          .delete(`${this.api}/remove/${name}`)
          .then(this.fetchCart)
          .catch((error) => console.error(error));
    },
  },
  mounted() {
    this.fetchCart();
  },
};
</script>

<style scoped>
.shopping-cart-container {
  max-width: 800px;
  margin: 0 auto;
  background-color: #faede3;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

h2 {
  color: #8b5d33;
}

.add-product {
  background-color: #f7f0e8;
}

.table {
  margin-bottom: 0;
}

.table th {
  background-color: #d8c1a6;
  color: #5a3d2b;
}

.total-summary {
  font-size: 1.1rem;
}

.total-summary p {
  margin: 0;
  color: #5a3d2b;
}
</style>