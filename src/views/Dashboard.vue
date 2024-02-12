<template>
  <Layout>
    <div class="min-h-screen bg-gray-100">
      <div class="max-w-6xl mx-auto py-6 sm:px-6 lg:px-8">
        <div class="px-4 py-6 sm:px-0">
          <div class="border-4 border-dashed border-gray-200 rounded-lg h-96">
            <!-- Table -->
            <div class="flex gap-4 mb-4">
              <input type="text" placeholder="Search by SKU" class="px-4 py-2 border rounded-md" v-model="searchSKU"
                @input="fetchItems()" />

              <input type="text" placeholder="Search by Name" class="px-4 py-2 border rounded-md" v-model="searchName"
                @input="fetchItems()" />

              <select v-model="searchStockStatus" @change="fetchItems()" class="px-4 py-2 border rounded-md">
                <option value="">All Statuses</option>
                <option value="IN">IN</option>
                <option value="OUT">OUT</option>
                <option value="BO">BO</option>
              </select>


            </div>
            <div class="flex flex-col">
              <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
                <div class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
                  <div class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg">
                    <table class="min-w-full divide-y divide-gray-200">
                      <thead class="bg-gray-50">
                        <tr>
                          <th scope="col"
                            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                            SKU
                            <button @click="updateSortOrder('SKU')">▲</button>
                            <button @click="updateSortOrder('-SKU')">▼</button>
                          </th>
                          <th scope="col"
                            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                            Name
                            <button @click="updateSortOrder('name')">▲</button>
                            <button @click="updateSortOrder('-name')">▼</button>
                          </th>
                          <th scope="col"
                            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                            Category
                            <button @click="updateSortOrder('category__name')">▲</button>
                            <button @click="updateSortOrder('-category__name')">▼</button>
                          </th>
                          <th scope="col"
                            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                            Stock Status

                          </th>
                          <th scope="col"
                            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                            In Stock
                            <button @click="updateSortOrder('in_stock')">▲</button>
                            <button @click="updateSortOrder('-in_stock')">▼</button>
                          </th>
                          <th scope="col"
                            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                            Available Stock
                            <button @click="updateSortOrder('available_stock')">▲</button>
                            <button @click="updateSortOrder('-available_stock')">▼</button>
                          </th>
                        </tr>
                      </thead>
                      <tbody class="bg-white divide-y divide-gray-200">
                        <tr v-for="item in items" :key="item.SKU">
                          <td class="px-6 py-4 whitespace-nowrap">
                            {{ item.SKU }}
                          </td>
                          <td class="px-6 py-4 whitespace-nowrap">
                            {{ item.name }}
                          </td>
                          <td class="px-6 py-4 whitespace-nowrap">
                            {{ item.category.name }}
                          </td>
                          <td class="px-6 py-4 whitespace-nowrap">
                            {{ item.stock_status }}
                          </td>
                          <td class="px-6 py-4 whitespace-nowrap">
                            {{ item.in_stock }}
                          </td>
                          <td class="px-6 py-4 whitespace-nowrap">
                            {{ item.available_stock }}
                          </td>
                        </tr>
                      </tbody>

                    </table>
                    <div class="py-3">
                      <div class="flex justify-between">
                        <button @click="fetchItems(previousPage)" :disabled="!previousPage"
                          class="px-4 py-2 bg-gray-300 text-gray-700 rounded hover:bg-gray-400 disabled:opacity-50">
                          Previous
                        </button>
                        <button @click="fetchItems(nextPage)" :disabled="!nextPage"
                          class="px-4 py-2 bg-gray-300 text-gray-700 rounded hover:bg-gray-400 disabled:opacity-50">
                          Next
                        </button>
                      </div>
                    </div>

                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </Layout>
</template>


<script setup>
import { onMounted, ref, watch } from 'vue';
import Layout from '@/components/Layout.vue'

const items = ref([]);
const searchSKU = ref('');
const searchName = ref('');
const sortOrder = ref('');
const searchStockStatus = ref('')
const nextPage = ref(null);
const previousPage = ref(null);
const currentSort = ref('');

const updateSortOrder = (field) => {
  currentSort.value = field;
  fetchItems();
};

const fetchItems = async (pageUrl) => {
  const token = localStorage.getItem('token');
  const params = new URLSearchParams({
    SKU: searchSKU.value,
    name: searchName.value,
    ordering: sortOrder.value,
    stock_status: searchStockStatus.value,
    page_size: 10,
    ordering: currentSort.value
  });

  const url = pageUrl || `https://kaizntree-dashboard-f671ec3afb12.herokuapp.com/api/items/?${params}`;

  try {
    const response = await fetch(url, {
      headers: {
        'Authorization': `Bearer ${token}`
      }
    });

    if (!response.ok) {
      throw new Error('Failed to fetch items');
    }

    const data = await response.json();
    items.value = data.results;
    nextPage.value = data.next;
    previousPage.value = data.previous;
  } catch (error) {
    console.error(error);
  }
};
watch([searchSKU, searchName, sortOrder], () => {
  fetchItems();
});

fetchItems();
</script>
