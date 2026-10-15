<script setup>
// Calendario de solo lectura (baseline). AGD-3 le conectará con las tareas.
const now = new Date();
const year = now.getFullYear();
const month = now.getMonth();
const first = new Date(year, month, 1);
const daysInMonth = new Date(year, month + 1, 0).getDate();
const offset = (first.getDay() + 6) % 7; // lunes = 0
const cells = Array.from({ length: offset + daysInMonth }, (_, i) =>
  i < offset ? null : i - offset + 1);
const monthName = now.toLocaleDateString('es', { month: 'long', year: 'numeric' });
</script>

<template>
  <section>
    <h2>{{ monthName }}</h2>
    <div class="grid">
      <b v-for="d in ['L','M','X','J','V','S','D']" :key="d">{{ d }}</b>
      <span v-for="(c, i) in cells" :key="i" class="cell">{{ c ?? '' }}</span>
    </div>
    <p class="hint">Vista de solo lectura — las tareas con fecha aparecerán aquí (AGD-3).</p>
  </section>
</template>

<style scoped>
.grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 2px; text-align: center; }
.cell { min-height: 2.2rem; padding: .3rem; border: 1px solid #eee; border-radius: 4px; }
.hint { color: #888; font-size: .85rem; margin-top: 1rem; }
</style>
