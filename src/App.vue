<template>
  <div class="grid grid-cols-1 container mx-auto justity-items-center">
    <img :src="require(`${picture[led_status].file}`)" :width="picture[led_status].size" 
    :height="picture[led_status].size" />
    <div class="h-12 my-5">
      <h3>LED Control Switch</h3>
    </div>
    <div class="h-10 space-x-5">
      <button class="mb-4 bg-green-500 text-white px-4 rounded focus:outline-none" @click="switchClicked(1)">
        Turn on
      </button>
      <button class="mb-4 bg-red-500 text-white px-4 rounded focus:outline-none" @click="switchClicked(0)">
        Turn off
      </button>
    </div>
  </div>

  <div class="grid grid-cols-1 container mx-auto justity-items-center">
    <img :src="require(`${picture1[led_status1].file}`)" :width="picture1[led_status1].size" 
    :height="picture1[led_status1].size" />
    <div class="h-12 my-5">
      <h3>LED Control Switch</h3>
    </div>
    <div class="h-10 space-x-5">
      <button class="mb-4 bg-green-500 text-white px-4 rounded focus:outline-none" @click="switchClicked1(1)">
        Turn on
      </button>
      <button class="mb-4 bg-red-500 text-white px-4 rounded focus:outline-none" @click="switchClicked1(0)">
        Turn off
      </button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import "./assets/tailwind.css"

export default {
  name: 'App',
  data() {
    return {
      led_status: 0,
      picture: [
        {
          file: "./assets/ledoff.png",
          size: 150,
        },
        {
          file: "./assets/ledon.png",
          size: 150,
        }
      ],
      led_status1: 0,
      picture1: [
        {
          file: "./assets/ledoff.png",
          size: 150,
        },
        {
          file: "./assets/ledon.png",
          size: 150,
        }
      ]
    }
  },
  mounted() {
    let apiurl = "https://api.netpie.io/v2/device/shadow/data";
    let headers = {
      "content-Type": "application/json",
      "Authorization": "Device f2bf6204-7c1e-4489-9db6-f3dd0222ca99:TvtCBXmHobkQqF7Mf4ekX9gvhJ23UP5G"

    };
    axios.get(apiurl, { headers })
      .then(response => {
        console.log(response.data);
        this.led_status = response.data.data.red_led;
      })
      .catch(error => {console.log(error)});
  },
  methods: {
    switchClicked(paramValue) {
      let _payload = '';
      if (paramValue == 1) {
        this.led_status = 1;
        _payload = "RED_ON";
      } else {
        _payload = "RED_OFF";
        this.led_status = 0;
      }
      this.publishDataToNetpie(_payload);
    },
    switchClicked1(paramValue) {
      let _payload = '';
      if (paramValue == 1) {
        this.led_status1 = 1;
        _payload = "GREEN_ON";
      } else {
        _payload = "GREEN_OFF";
        this.led_status1 = 0;
      }
      this.publishDataToNetpie(_payload);
    },
    publishDataToNetpie(_payload) {
      let targetTopic = "topic=lab_ict_kps%2Fcommand"
      let apiurl = "http://api.netpie.io/v2/device/message" + "?" + targetTopic
      let headers = {
        "Content-Type": "text/plain",
        "Authorization": "Device f2bf6204-7c1e-4489-9db6-f3dd0222ca99:TvtCBXmHobkQqF7Mf4ekX9gvhJ23UP5G"
      }
      let data = _payload;

      axios.put(apiurl, data, { headers })
        .then(response => { console.log(response) })
        .catch(error => { console.log(error) });
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
api.netpie.io