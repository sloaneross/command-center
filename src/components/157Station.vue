<template>
    <v-card
    class="mx-auto"
    max-width=100%
  >
    <v-card-text>
      <v-img
          src="../assets/1.png"
          max-width="25"
        ></v-img>
      <div class="text-h4 text--primary">157th Street</div>
    </v-card-text>
  
  <v-row
  no-gutters>
    <v-col
    align="center">
      <v-card>
        <v-card-text>
          <div>Uptown</div>
        </v-card-text>
        <v-list>
            <v-list-item
            v-for="arrival in northBound1"
            :key="arrival">
              <v-chip
              :color="getColor(arrival)">
              {{arrival.toFormat("m")}} min
              </v-chip>
            </v-list-item>
        </v-list>
      </v-card>
    </v-col>
    <v-col
    align="center">
      <v-card
      align="center">
        <v-card-text>
          <div>Downtown</div>
        </v-card-text>
        <v-list>
            <v-list-item
            v-for="arrival in southBound1"
            :key="arrival">
              <v-chip
              :color="getColor(arrival)">
              {{arrival.toFormat("m")}} min
              </v-chip>
            </v-list-item>
        </v-list>
      </v-card>
    </v-col>
  </v-row>
  </v-card>
</template>
  
<script lang='ts'>
import { stringLiteral } from '@babel/types'
import { defineComponent } from 'vue'
import {DateTime, Interval, Duration} from 'luxon'



export default defineComponent({
  
  name: 'OneFiveSevenCard',
  data () {
    return {
      northBound1: [] as Duration[],
      southBound1: [] as Duration[]
    }
  },
  computed: {
    getColor() {
      return (minutes: Duration) =>{
        if (minutes < Duration.fromMillis(180000)) return "red"
        else if (minutes < Duration.fromMillis(480000)) return "green"
        else return "grey"
      }
    }

  },
  mounted() {
    fetch("http://localhost:5001/by-id/7327")
      .then (response => response.json())
      .then (data => {
        const north :[] = data.data[0]["N"]
        const south :[] = data.data[0]["S"]
        this.northBound1 = north.map(arrival => Interval.fromDateTimes(DateTime.now(), DateTime.fromISO(arrival["time"])).toDuration()
        )
        this.southBound1 = south.map(arrival => Interval.fromDateTimes(DateTime.now(), DateTime.fromISO(arrival["time"])).toDuration()
        )
      })
    
    
  }
})


</script>
  