<template>
  <div class="header-container">
    <h2>Expense Tracker</h2>
    <button
      @click="toggleDark()"
      class="btn"
      :class="isDark ? 'btn-light' : 'btn-dark'"
    >
      {{ isDark ? 'Light' : 'Dark' }}
    </button>
  </div>
</template>

<script setup>
import { useDark, useToggle } from '@vueuse/core';

const isDark = useDark();
const toggleDark = useToggle(isDark);
</script>
