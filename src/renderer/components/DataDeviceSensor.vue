<template>
  <div id="devices-sensors-data">
    <h1>Devices, Sensors & Data</h1>
    <div id="device-data-status">
      <ul>
        <li>
          <header>TESTnetwork</header>
          <div id="hardware">
            <header>Hardware</header>
            <div v-if="firstTimetokenseen" id="askfor-token" >
              First time generation of token
                <first-token @load="text = $event" @removeGenbutton="firstGenerateseen($event)"></first-token>
            </div>
            <div v-if="repeatTimetokenseen" id="enter-token">
              Please navigate token file:
              <token-reader @load="text = $event" :viewPkey="viewPkey"> </token-reader>
            </div>
            <div id="hardware-firmware">
              OS Linux <a id="" href="">Cloud</a>
            </div>
            <div id="hardware-manufactureid">
              DigitalOcean
            </div>
          </div>
          <div id="data">
            <header>Data</header>
            <div id="data-location">
              TestServer
            </div>
            <div id="data-volume">

            </div>
          </div>
        </li>
        <device-list></device-list>
      </ul>
    </div>
  </div>
</template>

<script>
  import TokenReader from './LandingPage/token-reader.vue'
  import FirstToken from './LandingPage/token-first.vue'
  import deviceList from './healthscience/deviceData.vue'
  import fs from 'fs'

  export default {
    name: 'data-page',
    components: {
      TokenReader,
      FirstToken,
      deviceList
    },
    data: () => ({
      firstTimetokenseen: false,
      repeatTimetokenseen: false
    }),
    created () {
      this.checkforToken()
    },
    methods: {
      checkforToken () {
        // does a token file exist?
        const path = process.cwd() + '/keystore/healthscience-token.json'
        console.log(path)
        try {
          if (fs.existsSync(path)) {
            console.log('file found')
            // file exists start open decrypt
            this.repeatTimetokenseen = true
          } else {
            console.log('file err found')
            this.firstTimetokenseen = true
          }
        } catch (err) {
          console.error(err)
        }
      },
      firstGenerateseen (evSeen) {
        console.log('remove generation buutton')
        this.firstTimetokenseen = evSeen
        this.repeatTimetokenseen = true
        this.viewPkey = true
      }
    }
  }
</script>

<style>

#devices-sensors-data {
  margin: 1em;
}

#devices-sensors-data header {
  font-weight: bold;
}

#hardware {
  margin: 1em;
}

#hardware header {
  font-weight: bold;
}

#sensors {
  margin: 1em;
}

#sensors header {
  font-weight: bold;
}

#mobile {
  margin: 1em;
}

#mobile header {
  font-weight: bold;
}

#data {
  margin: 1em;
}

#data header {
  font-weight: bold;
}
</style>
