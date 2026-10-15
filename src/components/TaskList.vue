<script setup>
import { ref, onMounted } from 'vue';

const tasks = ref([]);
const error = ref('');

onMounted(async () => {
  try {
    const res = await fetch('/api/tasks');
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    tasks.value = await res.json();
  } catch (e) {
    error.value = `No se pudo cargar el listado: ${e.message}`;
  }
});
</script>

<template>
  <section>
    <h2>Tareas pendientes</h2>
    <p v-if="error" class="error">{{ error }}</p>
    <ul v-else>
      <li v-for="t in tasks" :key="t.id" :class="{ done: t.done }">
        <span>{{ t.title }}</span>
        <em v-if="t.done">✔</em>
      </li>
    </ul>
  </section>
</template>

<style scoped>
ul { list-style: none; padding: 0; }
li { display: flex; justify-content: space-between; padding: .6rem .8rem; border-bottom: 1px solid #eee; }
li.done span { text-decoration: line-through; color: #888; }
.error { color: #b00; }
</style>
