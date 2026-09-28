<script setup>
import { ref } from "vue";

defineProps({
  user: {
    type: Object,
    required: true,
  },
  sorotan: {
    type: Boolean,
    default: false,
  },
});

// --- Problem 1: Local state untuk buka/tutup detail ---
const detailTerbuka = ref(false);

function toggleDetail() {
  detailTerbuka.value = !detailTerbuka.value;
}
</script>

<template>
  <li class="user-card" :class="{ 'user-card-highlight': sorotan }">
    <div class="user-header">
      <div>
        <strong>{{ user.name }}</strong>
        <span>{{ user.email }}</span>
      </div>
      <button @click="toggleDetail" class="btn-detail">
        {{ detailTerbuka ? "Sembunyikan" : "Lihat Detail" }}
      </button>
    </div>

    <div v-if="detailTerbuka" class="user-detail">
      <p><strong>Telepon:</strong> {{ user.phone }}</p>
      <p><strong>Perusahaan:</strong> {{ user.company.name }}</p>
      <p><strong>Kota:</strong> {{ user.address.city }}</p>
    </div>
  </li>
</template>

<style scoped>
.user-card {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  padding: 0.75rem 1rem;
  border-bottom: 1px solid #e0e0e0;
}

.user-card-highlight {
  background: #fff8b0;
  border-left: 4px solid #f1c40f;
}

.user-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
}

.user-header > div {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.btn-detail {
  padding: 0.4rem 0.8rem;
  border: none;
  border-radius: 6px;
  background: #0b4f6c;
  color: #fff;
  cursor: pointer;
  font-size: 0.85rem;
  font-weight: bold;
  white-space: nowrap;
}

.btn-detail:hover {
  background: #056688;
}

.user-detail {
  background: #f9f9f9;
  padding: 0.5rem 0.75rem;
  border-radius: 6px;
  font-size: 0.9rem;
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.user-detail p {
  margin: 0;
}
</style>