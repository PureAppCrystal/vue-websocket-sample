<template>
  <div class="basic">

    <div>
      WebSocket Basic Sample Page
    </div>

    <button @click="disconnect" v-if="status === 'connected'"> disconnect </button>
    <button @click="connect" v-if="status === 'disconnected'"> connect </button> {{status}}

    <div v-if="status ==='connected'">
      <form @submit.prevent="sendMessage" action="#">
        <input v-model="message"/> <button type="submit">send Message</button>
      </form>
      <ul id="logs">
        <li v-for="log in logs" class="log" v-bind:key="log.data">
          {{log.event}} : {{ log.data}}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BasicSample',
  props: {
    msg: String
  },
  data() {
    return {
      status: 'disconnected',
      wsUri: "wss://echo.websocket.org/",
      message: "",
      logs: [],
    }
  },
  computed: {

  },
  methods: {
    test() {

    },
    test1: function(test) {
      // eslint-disable-next-line no-console
      console.log(test);
    },
    
    connect() {
      this.socket = new WebSocket(this.wsUri);
      this.socket.onopen = () => {
        console.log("====== onopen ======");
        this.status = "connected"
        this.logs.push({event: "연결 완료: ", data: this.wsUri})

        this.socket.onmessage = ({data}) => {
          this.logs.push({event: "메세지 수신", data})
        }

        this.socket.onclose = ({evt}) => {
          console.log("onclose : ", evt)
          this.status = "disconnected";
        }

        this.socket.onerror = ({evt}) => {
          console.log("onerror : ", evt)
        }
      }
    },

    disconnect() {
      this.socket.close();
      this.status = "disconnected";
      this.logs = [];
    },

    sendMessage(e) {
      this.socket.send(this.message);
      this.logs.push({event: "메세지 전송", data: this.message});
      this.message = "";
    }

  },
  created() {
    
  },
  mounted() {

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
li { width: 100%;}
</style>
