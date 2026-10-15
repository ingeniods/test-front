<script setup>
import { ref } from 'vue';
import TaskList from './components/TaskList.vue';
import CalendarView from './components/CalendarView.vue';

const tab = ref('tasks');
</script>

<template>
  <div class="app">
    <h1>Agenda</h1>
    <nav>
      <button :class="{ active: tab === 'tasks' }" @click="tab = 'tasks'">Tareas</button>
      <button :class="{ active: tab === 'calendar' }" @click="tab = 'calendar'">Calendario</button>
    </nav>
    <TaskList v-if="tab === 'tasks'" />
    <CalendarView v-else />
  </div>
</template>

<style scoped>
.app { max-width: 720px; margin: 2rem auto; font-family: system-ui, sans-serif; }
nav { display: flex; gap: .5rem; margin-bottom: 1.5rem; }
button { padding: .5rem 1rem; border: 1px solid #ccc; background: #fff; border-radius: 6px; cursor: pointer; }
button.active { background: #334; color: #fff; }
</style>
