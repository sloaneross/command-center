<template>
  <WeatherCard/>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

// Components
import WeatherCard from '../components/WeatherCard.vue';

export default defineComponent({
  name: 'AboutView',

  components: {
    WeatherCard,
  },
});
</script>