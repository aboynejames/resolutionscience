<template>
  <div id="key-system">
    <div id="key-verify" v-if="fileinputSeen">
      <label class="text-reader">
        Read Key File
        <input type="file" @change="loadTextFromFile">
      </label>
    </div>
    <div id="pwinput-prompt" v-if="pwinputSeen">
      Please enter password
      <passwordk v-model="passwordk" :toggle="true" />
      <button @click.prevent="verifyKeypw" class="button is-primary">Verify key ownership</button>
    </div>
    <div id="keypw-feedback">{{ verifyfeedbackM }}</div>
  </div>
</template>

<script>
  import Passwordk from 'vue-password-strength-meter'
  // import FileReader from './LandingPage/file-reader.vue'
  import keythereum from 'keythereum'

  export default {
    name: 'unlockkey-page',
    components: {
      Passwordk,
      FileReader
    },
    data: () => ({
      pkaddress: '',
      keyObject: {},
      verifyfeedbackM: '',
      fileinputSeen: true,
      pwinputSeen: false,
      passwordk: null,
      text: '',
      keybuttonseen: false,
      feedbackM: '',
      warningM: ''
    }),
    methods: {
      loadTextFromFile (ev) {
        // prompt for Password
        const filepath = ev.target.files[0].path
        const extractPkey = filepath.substr(filepath.length - 40)
        this.pkaddress = extractPkey
        this.$store.commit('setPublickey', this.pkaddress)
        // Specify a data directory (optional; defaults to ~/.ethereum)
        var datadir = process.cwd()
        this.keyObject = keythereum.importFromFile(this.pkaddress, datadir)
        this.pwinputSeen = true
      },
      verifyKeypw () {
        // verify key password
        try {
          var privateKey = keythereum.recover(this.passwordk, this.keyObject)
        } catch (err) {
          this.verifyfeedbackM = 'Password not correct.'
        }
        // console.log(privateKey)
        this.passwordk = ''
        // success
        if (privateKey) {
          // passed
          this.$emit('removeCreatekey', false)
          this.fileinputSeen = false
          this.pwinputSeen = false
          this.verifyfeedbackM = 'Key has been verifed.'
          // set public key startTime
          // const tokenJSONsetP = {}
          // tokenJSONsetP.publickey = this.pkaddress
          // remove create key via events

          this.$store.commit('setPublickey', this.pkaddress)
        } else {
          // password failed
          this.verifyfeedbackM = 'Password not correct.'
        }
      }
    }
  }
</script>

<style>
.text-reader {
  position: relative;
  overflow: hidden;
  display: inline-block;

  /* Fancy button looking */
  border: 2px solid black;
  border-radius: 5px;
  padding: 8px 12px;
  cursor: pointer;
}
.text-reader input {
  position: absolute;
  top: 0;
  left: 0;
  z-index: -1;
  opacity: 0;
}
#pwinput-prompt {
  text-align: center;
}
.is-primary {
  font-size: 1.4em;
}
#keypw-feedback {
  font-size: 1.4em;
  text-align: center;
}
</style>
