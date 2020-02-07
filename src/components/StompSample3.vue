<template>
    <div>
      <button v-on:click="getRoomList()" >get Room List</button>
      <div id="room-list">
        <div>RoomList</div>
        <div class="room" v-for="room in roomList" v-bind:key="room.id" v-on:click="selectRoom(room)">{{room.name}}</div>
      </div>

      <div id="select-room" >        
        <div v-if="selectRoomId !== null" >
          <div>NAME : {{selectRoomName}}</div>
          <div>ID : {{selectRoomId}}</div>
          <button v-on:click="chatJoin()">join</button>
          <button v-on:click="checkClient()">checkClient</button>
          <button v-on:click="trySubscribe()">trySubscribe</button>
          
        </div>
      </div>

      <div id="chat-area">
        <ul class="chat_box">
          <li v-for="msg in messageList" v-bind:key="msg.id">{{msg}}</li>
        </ul>
        <form @submit.prevent="sendMessage" action="#">
          <input v-model="send_message"/> <button type="submit">send Message</button>
        </form>
      </div>

    </div>
</template>

<script>
import SockJS from "sockjs-client";
import Stomp from "webstomp-client";
import Axios from "axios";
export default {
  name: "StompSample2",
  data() {
    return {
      serverUrl: "http://localhost:5001",
      roomList: null,
      selectRoomId: null,
      selectRoomName: null,
      member: null,
      client: null,
      messageList: [],

      received_messages: [],
      send_message: null,
      connected: false
    };
  },
  methods: {
    async getRoomList() {
      const result = await Axios.post(this.serverUrl+"/chat/getRooms", {})
      console.log("result : ", result);
      if (result.status === 200 && result.data !== undefined && result !== null) {
        this.roomList = result.data;

      }
    },
    selectRoom(room) {
      this.selectRoomId = room.id;
      this.selectRoomName = room.name;
    },
    async chatJoin() {
      console.log("====== chat Join ======")
      const roomId = this.selectRoomId;
      
      // 방정보 들어가기 정보 
      const result = await Axios.post(this.serverUrl+"/chat/rooms/"+roomId);
      console.log("result : ", result);
      if( result.status !== 200 || result.data == null || result.data === undefined) {
        console.log("request /chat/rooms/{id} failed")
        return;
      }
      this.member = result.data.member;
      const member = this.member;
      const room = result.data.room;
      let list = this.messageList
      
      this.sock = new SockJS(this.serverUrl+"/gs-guide-websocket");
      this.client = Stomp.over(this.sock); // 1. SockJS를 내부에 들고 있는 client를 내어준다.

      // 2. connection이 맺어지면 실행된다.
      this.client.connect({}, frame=> {

          console.log("frame : ", frame);
          // 3. send(path, header, message)로 메시지를 보낼 수 있다.
          //this.client.send(this.serverUrl+'/publish/chat/join', {}, JSON.stringify({chatRoomId: roomId, writer: member, message: "", type: "JOIN"})); 
          // 4. subscribe(path, callback)로 메시지를 받을 수 있다. callback 첫번째 파라미터의 body로 메시지의 내용이 들어온다.
          // this.client.subscribe('/topic/greetings', function (chat) {
          //this.client.subscribe('/topic/a416576b-3c24-4da6-9166-57cc98ce0cc7', function (chat) {
            
          this.client.subscribe('/topic/room/'+roomId, function (chat) {
          //this.client.subscribe(this.serverUrl+'/subscribe', function (chat) {
            console.log("====== subscribe ======")
              console.log("chat : ", chat);
              var content = JSON.parse(chat.body);
              
              list.push(content.content);
              //const chatBox = document.getElementsByClassName("chat_box")
              //chatBox.append('<li>' + content.message + '(' + content.writer + ')</li>')
          });
      });
    },
    initClient() {

    },
    trySubscribe() {
      console.log("====== trySubscribe ======")
      const roomId = this.roomId;

      this.client.subscribe(this.serverUrl+'/subscribe/chat/room/' + roomId, function (chat) {
        console.log("====== subscribe ======")
          console.log("chat : ", chat);
          var content = JSON.parse(chat.body);
          
          const chatBox = document.getElementsByClassName("chat_box")
          chatBox.append('<li>' + content.message + '(' + content.writer + ')</li>')
      });
    },
    checkClient() {
      console.log("====== checkClient ======")
      console.log("client : ", this.client);
    },
    sendMessage() {
      console.log("====== sendMessage ======")
      const message = this.send_message;
      console.log("roomId : ", this.roomId)
      console.log("message : ", message);
      //this.client.send(this.serverUrl+'/publish/chat/message', {}, JSON.stringify({chatRoomId: this.roomId, type: 'CHAT', message: message, writer: this.member}));
      // this.client.send("/app/hello", JSON.stringify({name: message}));
      //this.client.send("/app/room2", JSON.stringify({name: message}));
      this.client.send("/app/chat/"+this.selectRoomId, JSON.stringify({name: message}));
      this.send_message = "";
    }
  }
  
    
};
</script>

<style scoped>

</style>