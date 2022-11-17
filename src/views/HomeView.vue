<template>
  <v-container
  >
    <v-row
    max-width="100"
    >
      <v-col
      cols="12">
        <WeatherCard/>
      </v-col>
    </v-row>
    <v-row>
      <v-col
      cols="6">
        <OneFiveSevenCard/>
      </v-col>
      <v-col
      cols="6">
        <OneFiveFiveCard/>
      </v-col>      
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

// Components
import WeatherCard from '../components/WeatherCard.vue'
import OneFiveFiveCard from '../components/OneFiveFiveStation.vue'
import OneFiveSevenCard from '../components/157Station.vue'

export default defineComponent({
  name: 'HomeView',

  components: {
    WeatherCard,
    OneFiveFiveCard,
    OneFiveSevenCard
  },
});
</script>