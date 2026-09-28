<script setup>
import { ref, computed, watch } from "vue";
import UserCard from "./components/UserCard.vue";

const judul = "Dashboard Vue - Pertemuan 5";

const users = ref([]);
const keadaan = ref("idle");

async function muatPengguna() {
  keadaan.value = "loading";

  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");

    if (!response.ok) {
      throw new Error("Status HTTP: " + response.status);
    }

    const data = await response.json();

    if (data.length === 0) {
      keadaan.value = "empty";
      return;
    }

    users.value = data;
    keadaan.value = "success";
  } catch (error) {
    console.error("Gagal memuat data:", error);
    keadaan.value = "error";
  }
}

// --- Step 8.2: Deklarasi baru ---
const queryPencarian = ref("");
const riwayatPencarian = ref([]);

const penggunaTersaring = computed(() => {
  const q = queryPencarian.value.toLowerCase().trim();
  if (!q) return users.value;
  return users.value.filter((user) =>
    user.username.toLowerCase().includes(q)
  );
});

// --- Exercise A: Result Count Summary ---
const jumlahHasil = computed(() => penggunaTersaring.value.length);

// --- Problem 2: Sort Control (Chained Computed) ---
const arahUrutan = ref("asc"); // 'asc' atau 'desc'

const penggunaTerurut = computed(() => {
  const salinan = [...penggunaTersaring.value]; // salin array dulu
  return salinan.sort((a, b) => {
    if (arahUrutan.value === "asc") {
      return a.name.localeCompare(b.name);
    } else {
      return b.name.localeCompare(a.name);
    }
  });
});

watch(queryPencarian, (nilaiBaru) => {
  if (nilaiBaru.trim() !== "") {
    riwayatPencarian.value.push(nilaiBaru);
  }
});
</script>

<template>
  <header class="app-header">
    <h1>{{ judul }}</h1>
  </header>
  <main class="app-main">
    <button @click="muatPengguna">Muat Pengguna</button>

    <!-- Step 8.3: Input pencarian — letakkan SEBELUM rantai v-if -->
    <input v-model="queryPencarian" type="text" placeholder="Cari username..." />

    <div class="sort-controls">
      <button
        @click="arahUrutan = 'asc'"
        :class="{ active: arahUrutan === 'asc' }"
  >
        Urutkan A-Z
    </button>
      <button
        @click="arahUrutan = 'desc'"
        :class="{ active: arahUrutan === 'desc' }"
  >
        Urutkan Z-A
      </button>
    </div>

    <p v-if="keadaan === 'loading'">Memuat data...</p>
    <p v-else-if="keadaan === 'empty'">Tidak ada pengguna ditemukan.</p>
    <p v-else-if="keadaan === 'error'">Gagal memuat data. Coba lagi.</p>
    <div v-else-if="keadaan === 'success'">
      <p>Menampilkan {{ jumlahHasil }} dari {{ users.length }} pengguna</p>
      <ul>
        <UserCard
          v-for="user in penggunaTerurut"
          :key="user.id"
          :user="user"
          :sorotan="user.username.toLowerCase() === queryPencarian.toLowerCase().trim()"
        />
      </ul>
    </div>
  </main>
</template>

<style scoped>
.app-header {
  padding: 1.5rem;
  background: #0b4f6c;
  color: white;
}
.app-main {
  padding: 1.5rem;
}
.sort-controls {
  display: flex;
  gap: 0.5rem;
  margin: 0.5rem 0;
}

.sort-controls button {
  padding: 0.4rem 0.8rem;
  border: 1px solid #0b4f6c;
  border-radius: 6px;
  background: #fff;
  color: #0b4f6c;
  cursor: pointer;
  font-weight: bold;
}

.sort-controls button:hover {
  background: #eef5f7;
}

.sort-controls button.active {
  background: #0b4f6c;
  color: #fff;
}
</style>