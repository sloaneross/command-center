<template>
  <v-app>
    <v-main>
      <router-view/>
    </v-main>
  </v-app>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import "./assets/styles.css"
import { DateTime } from "luxon"

export default defineComponent({
  name: 'App',

  data () {
    return {
      //
    }
  },
})
</script>
