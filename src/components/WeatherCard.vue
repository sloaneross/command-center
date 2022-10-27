<template>
    <v-card
      width="400"
      title="This is a title"
      :subtitle=temperature
      text="This is content"
    ></v-card>
</template>
  
<script lang='ts'>
import { defineComponent } from 'vue'


export default defineComponent({
  name: 'WeatherCard',
  data () {
    return {
      currentTemp: 3
    }
  },
  computed: {
    temperature():number {
      return this.currentTemp
    }
  },
  created() {
    fetch("https://api.weather.gov/stations/knyc/observations/latest?require_qc=false")
      .then (response => response.json())
      .then (data => {
        const temp = (+data.properties.temperature.value * 1.8) + 32
        console.log(temp)
        this.currentTemp = temp
      })
  }
})
</script>
  