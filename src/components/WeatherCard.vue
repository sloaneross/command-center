<template>
    <v-card
    class="mx-auto"
    max-width=33%
  >
    <v-card-item title="New York City">
      <template v-slot:subtitle>
        <v-icon
          icon="mdi-alert"
          size="18"
          color="error"
          class="mr-1 pb-1"
        ></v-icon>

        {{weather}}
      </template>
    </v-card-item>

    <v-card-text class="py-0">
      <v-row align="center" no-gutters>
        <v-col
          class="text-h2"
          cols="9"
        >
          {{temperature}}&deg;F
        </v-col>
        <v-col cols="3">
          <v-img
            :src=currentWeatherIcon
            :alt=weather
            width="92"
          ></v-img>
        </v-col>
      </v-row>
    </v-card-text>

    <div class="d-flex py-3 justify-space-between">
      <v-list-item
        density="compact"
        prepend-icon="mdi-weather-windy"
      >
        <v-list-item-subtitle>123 km/h</v-list-item-subtitle>
      </v-list-item>

      <v-list-item
        density="compact"
        prepend-icon="mdi-weather-pouring"
      >
        <v-list-item-subtitle>48%</v-list-item-subtitle>
      </v-list-item>
    </div>

    <v-expand-transition>
      <div v-if="expand">
        <div class="py-2">
          <v-slider
            v-model="time"
            :ticks="labels"
            :max="forecasts.length -1"
            :step="1"
            class="mx-4"
            color="primary"
            density="compact"
            hide-details
            show-ticks="always"
            thumb-size="10"
          ></v-slider>
        </div>

        <v-list class="transparent">
          <v-list-item
            :title="forecasts[time].shortForecast"
            :subtitle="forecasts[time].temperature"
          >
          <template v-slot:prepend>
            <v-img
              :src=forecasts[time].icon
              :alt=forecasts[time].shortForecast
              width="92"
          ></v-img>
          </template>
          </v-list-item>
        </v-list>
      </div>
    </v-expand-transition>

    <v-divider></v-divider>

    <v-card-actions>
      <v-btn @click="expand = !expand">
        {{ !expand ? 'Full Report' : 'Hide Report' }}
      </v-btn>
    </v-card-actions>
  </v-card>
</template>
  
<script lang='ts'>
import { stringLiteral } from '@babel/types'
import { defineComponent } from 'vue'

interface Forecast{
  position: number
  name: string
  startTime: Date
  endTime: Date
  temperature: number
  windSpeed: string
  windDirection: string
  shortForecast: string
  longForecast: string
  icon: string
} 


export default defineComponent({
  name: 'WeatherCard',
  data () {
    return {
      currentTemp: 3,
      forecasts: [] as Forecast[],
      currentIcon: "",
      currentWeather: "Loading",
      expand: false,
      time: 0,
      labels: {}
    }
  },
  computed: {
    temperature():number {
      return +this.currentTemp.toPrecision(4)
    },
    weather():string {
      return this.currentWeather
    },
    currentWeatherIcon ():string {
      return this.currentIcon
    },
  },
  created() {
    fetch("https://api.weather.gov/stations/knyc/observations/latest?require_qc=false")
      .then (response => response.json())
      .then (data => {
        const temp = (+data.properties.temperature.value * 1.8) + 32
        this.currentTemp = temp
        this.currentIcon = data.properties.icon as string
        this.currentWeather = data.properties.textDescription as string
      })
    
    fetch ("https://api.weather.gov/gridpoints/OKX/34,40/forecast")
      .then (response => response.json())
      .then (data => {
        const periods: [] = data.properties.periods
        const periodsMapped = periods.map(forecast => 
          ({
            position: +forecast["number"] -1,
            name: forecast["name"],
            startTime: new Date(forecast["startTime"]),
            endTime: new Date(forecast["endTime"]),
            temperature: +forecast["temperature"],
            windSpeed: forecast["windSpeed"],
            windDirection: forecast["windDirection"],
            shortForecast: forecast["shortForecast"],
            longForecast: forecast["detailedForecast"],
            icon: forecast["icon"]
          }) as Forecast
        ).slice(0,6)
        this.forecasts = periodsMapped
        
        const timeLabels = periodsMapped.reduce(
          (acc, forecast) => {
              acc[forecast.position.toString()] = forecast.name
              return acc
            },
          {} as Record<string, string>
        )
        this.labels = timeLabels
      })
  }
})


</script>
  