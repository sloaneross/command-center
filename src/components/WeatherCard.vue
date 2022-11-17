<template>
  <v-card
  min-width="200px"
  min-height="200px"
>
  <v-img
  class="white-text alight-end"
  height="150px"
  cover
  :src="currentIcon">
    <v-card-title class="color:white">New York City</v-card-title>
  </v-img>
    <v-card-subtitle>
      {{weather}}
    </v-card-subtitle>
  <v-card-text class="py-0">
    <v-row align="center" no-gutters>
      <v-col
        class="text-h2"
        cols="9"
      >
        {{temperature}}&deg;F
      </v-col>
    </v-row>
  </v-card-text>

  <div class="d-flex py-3 justify-space-between">
    <v-list-item
      density="compact"
      prepend-icon="mdi-weather-windy"
    >
      <v-list-item-subtitle>{{currentWind}} m/h</v-list-item-subtitle>
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
          ></v-slider>
      </div>
      <br>
      <v-row>
        
        <v-col>
          <v-card>
            <v-card-title>
              {{forecasts[time].shortForecast}}
            </v-card-title>
            <v-card-subtitle>
              {{forecasts[time].temperature}}
            </v-card-subtitle>
            <br/>
            <v-row>
              <v-col>
                <div class="justify-space-between">
                  <v-list-item
                    density="compact"
                    prepend-icon="mdi-weather-windy"
                  >
                    <v-list-item-subtitle>{{forecasts[time].windSpeed}}</v-list-item-subtitle>
                  </v-list-item>
                  
                  <v-list-item>
                    <p>
                      {{forecasts[time].longForecast}}
                    </p>
                  </v-list-item>
                </div>
              </v-col>
              
              <v-col>
                <v-img
                align="center"
                :src=forecasts[time].icon
                :alt=forecasts[time].shortForecast
                cover
                width="150">
                </v-img>
              </v-col>
              
            </v-row>
            
          </v-card>
        </v-col>
        <v-spacer/>
      </v-row>
      

      
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
import {DateTime, Interval} from 'luxon'

interface Forecast{
position: number
name: string
startTime: DateTime
endTime: DateTime
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
    currentWind: 0,
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
  }
},
mounted() {
  fetch("https://api.weather.gov/stations/knyc/observations/latest?require_qc=false")
    .then (response => response.json())
    .then (data => {
      const temp = (+data.properties.temperature.value * 1.8) + 32
      this.currentTemp = temp
      this.currentIcon = data.properties.icon as string
      this.currentWeather = data.properties.textDescription as string
      this.currentWind = +((+data.properties.windSpeed.value ) / 1.609344 ).toPrecision(3)
    })
  
  fetch ("https://api.weather.gov/gridpoints/OKX/34,40/forecast")
    .then (response => response.json())
    .then (data => {
      const periods: [] = data.properties.periods
      const periodsMapped = periods.map(forecast => 
        ({
          position: +forecast["number"] -1,
          name: forecast["name"],
          startTime: DateTime.fromISO(forecast["startTime"]),
          endTime: DateTime.fromISO(forecast["endTime"]),
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
            acc[forecast.position.toString()] = forecast.endTime.toLocaleString({hour: 'numeric',  weekday: 'short'})
            return acc
            },
          {} as Record<string, string>
        )
        this.labels = timeLabels
      })
},
})


</script>
