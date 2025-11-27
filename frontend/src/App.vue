<template>
  <div class="min-h-screen bg-gray-100 p-8 text-gray-900">
    <h1 class="text-3xl font-bold text-center mb-8 text-blue-600">School Supply Store</h1>

    <!-- FORM: Add New Product -->
    <div class="max-w-md mx-auto bg-white p-6 rounded shadow mb-8">
      <h2 class="text-xl font-semibold mb-4">Add Product</h2>
      <input v-model="form.name" type="text" placeholder="Product Name" class="w-full border p-2 mb-2 rounded">
      <input v-model="form.price" type="number" placeholder="Price" class="w-full border p-2 mb-2 rounded">
      <input v-model="form.imageUrl" type="text" placeholder="Image URL" class="w-full border p-2 mb-4 rounded">
      <button @click="saveProduct" class="w-full bg-green-500 text-white py-2 rounded font-bold hover:bg-green-600">
        Save to MongoDB
      </button>
    </div>

    <!-- LIST: Display Products -->
    <div v-if="products.length === 0" class="text-center text-gray-500">Loading products...</div>
    
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
      <div v-for="product in products" :key="product.id" class="bg-white p-4 rounded shadow hover:shadow-lg transition">
        <img :src="product.imageUrl || 'https://via.placeholder.com/150'" class="w-full h-40 object-cover mb-4 rounded">
        <h3 class="text-lg font-bold">{{ product.name }}</h3>
        <p class="text-gray-600">${{ product.price }}</p>
        <button class="mt-3 w-full bg-blue-500 text-white py-2 rounded">Add to Cart</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const products = ref([])
const form = ref({ name: '', price: '', imageUrl: '' })

// 1. Fetch data from Spring Boot
const fetchProducts = async () => {
  try {
    const res = await axios.get('http://localhost:8080/api/products')
    products.value = res.data
  } catch (error) {
    console.error("Connection Error:", error)
  }
}

// 2. Send data to Spring Boot
const saveProduct = async () => {
  if(!form.value.name || !form.value.price) return alert("Please fill details")
  
  await axios.post('http://localhost:8080/api/products', form.value)
  
  // Clear form and refresh list
  form.value = { name: '', price: '', imageUrl: '' }
  fetchProducts()
}

// Run on page load
onMounted(() => {
  fetchProducts()
})
</script>
