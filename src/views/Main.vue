<template>
<div class ="contain">
  <div class="game-log-message-box">
        <h3>Enter chat message</h3>
        <form @submit.prevent="sendMessage">
            <label for="chat-message"></label>
            <textarea v-model="message" id="chat-message" rows="4" cols="45" autofocus>
              Enter your message here...
            </textarea><br>
            <button type="submit" class="submit-button">Submit!</button>
        </form>
  </div>
  <link href="https://fonts.googleapis.com/css?family=Montserrat" rel="stylesheet">

  <div class="drawer">

    <header>
      <p class="message">Tic Tac Toe</p>
      <button class="play-btn">play</button>
    </header>

    <main class="board">

      <div v-if ="position1 === 'A'" class="cell circle"></div>
      <div v-else-if="position1 === 'B'" class="cell cross"></div>
      <div v-else class="cell" @click="choose(1)"></div>

      <div v-if ="position2 === 'A'" class="cell circle"></div>
      <div v-else-if="position2 === 'B'" class="cell cross"></div>
      <div v-else class="cell" @click="choose(2)"></div>

      <div v-if ="position3 === 'A'" class="cell circle"></div>
      <div v-else-if="position3 === 'B'" class="cell cross"></div>
      <div v-else class="cell" @click="choose(3)"></div>

     <div v-if ="position4 === 'A'" class="cell circle"></div>
      <div v-else-if="position4 === 'B'" class="cell cross"></div>
      <div v-else class="cell" @click="choose(4)"></div>

     <div v-if ="position5 === 'A'" class="cell circle"></div>
      <div v-else-if="position5 === 'B'" class="cell cross"></div>
      <div v-else class="cell" @click="choose(5)"></div>

      <div v-if ="position6 === 'A'" class="cell circle"></div>
      <div v-else-if="position6 === 'B'" class="cell cross"></div>
      <div v-else class="cell" @click="choose(6)"></div>

      <div v-if ="position7 === 'A'" class="cell circle"></div>
      <div v-else-if="position7 === 'B'" class="cell cross"></div>
      <div v-else class="cell" @click="choose(7)"></div>

      <div v-if ="position8 === 'A'" class="cell circle"></div>
      <div v-else-if="position8 === 'B'" class="cell cross"></div>
      <div v-else class="cell" @click="choose(8)"></div>

      <div v-if ="position9 === 'A'" class="cell circle"></div>
      <div v-else-if="position9 === 'B'" class="cell cross"></div>
      <div v-else class="cell" @click="choose(9)"></div>
    </main>

    <footer class="scores hide">

      <div>
        <span></span>
        <ul>
          <li></li>
          <li></li>
          <li></li>
        </ul>
      </div>

      <div>
        <span></span>
        <ul>
          <li></li>
          <li></li>
          <li></li>
        </ul>
      </div>

    </footer>
  </div>
  <div class="game-log-message-box">
      <p>{{winner}}</p>
      <p>{{msg}}</p>
      <div id="separatorLine"></div>
      <div style="display:flex; flex-direction: column-reverse;">
        <div class="game-log-chat-messages">
          <div v-for="message in chatMessages" :key="message.name">
            <p><b>{{message.name}}</b></p>
            <p>{{message.message}}</p>
          </div>
        </div>
      </div>
    </div>
</div>
</template>

<script>
import io from 'socket.io-client'
import axios from 'axios'
import { mapState, mapActions } from 'vuex'
export default {
  name: 'chatBox',
  data () {
    return {
      winner: '',
      message: '',
      msg: '',
      chatMessages: [],
      userpick: '',
      islogin: false
    }
  },
  computed: {
    ...mapState(['userrun', 'position1', 'position2', 'position3', 'position4', 'position5', 'position6', 'position7', 'position8', 'position9'])
  },
  methods: {
    ...mapActions(['fetchposition']),
    sendMessage () {
      console.log('chat message submitted')
      const messageData = {
        name: localStorage.username,
        message: this.message
      }
      var socket = io.connect('http://localhost:3000')
      socket.emit('send-message', messageData)
      this.message = ''
    },
    choose (pick) {
      console.log(pick)
      axios({
        method: 'PUT',
        url: 'http://localhost:3000/data',
        headers: {
          room: localStorage.room,
          username: localStorage.username
        },
        data: {
          chose: pick
        }
      })
        .then(result => {
          this.fetchposition()
          var socket = io.connect('http://localhost:3000')
          socket.emit('refresh')
        })
        .catch(err => {
          console.log(err)
        })
    }
  },
  created () {
    this.fetchposition()
    if (this.userrun % 2 === 0) {
      console.log('GILIRAN PEMAIN GANJIL')
    } else {
      console.log('GILIRAN PEMAIN GENAP')
    }
    console.log('data dari main', this.position1, this.position2)

    io.connect('http://localhost:3000').on('send-message', (data) => {
      console.log('kumpulan chat message diterima')
      this.chatMessages = data
    })

    io.connect('http://localhost:3000').on('refresh_client', () => {
      this.fetchposition()
    })

    if (localStorage.username) {
      this.islogin = true
    } else {
      this.islogin = false
      this.$router.push('/')
    }

    // io.on('connected', function (username) {
    //   this.msg = 'User ' + username + '  has joined'
    // })
  }
}
</script>

<style>

.contain {
  display: flex;
  justify-content: space-evenly;
}
.chatBox{
  border:1px;
  height: 300px;
  border-style: solid;
  border-color: #0d0e0d;
  background-color:#ecf0f1;
}

.submit-button{
  background-color:#2ecc71;
  color:white;
  border-width: 0px;
  padding: 10px 25px;
  margin: 10px;
  border-radius: 4px;
}
.game-log-message-box{
  height: 80%;
  background-color: width;
  width: 80%;
  text-align: left;
}

.game-log-box{
  height: 100%;
  border:1px;
  border-style: solid;
  border-color: #020202;
  background-color:#ecf0f1;
  padding: 5px 20px;
  overflow: hidden;
  text-overflow: ellipsis;
}

.game-log-box-header{
  display:flex;
  justify-content: space-between;
}

.game-log-chat-messages{
  border: solid black 1px;
  height: 300px;
  width: 100%;
  text-overflow: ellipsis;
  overflow: scroll;
  overflow-x: hidden;
}

.textarea{
  width: 80%;
}

*, *::after, *::before {
      box-sizing: border-box;
    }

html {
  font-size:62.5%;
}

body {
  font-size:1.6rem;
  margin:0;
  height:100vh;
  background:hsl(300, 15%, 36%);
  font-family: 'Montserrat', 'Arial', sans-serif;
  letter-spacing: 1px;
}

.drawer {
  width: 80%;
  margin:0 auto;
  padding-top:60px;
}

.board {
  display:flex;
  flex-wrap: wrap;
  width: 320px;
  height: 320px;
  margin: 0 auto;
}

.cell {
  position: relative;
  width:90px;
  height:90px;
  margin:5px;
  border-radius: 0.3em;
  background:hsl(300, 15%, 33%);
}

.cell.circle,
.cell.cross {
  background:transparent;
}

.circle::after,
.cross::before,
.cross::after {
  content:'';
  position:absolute;
  top:50%;
  left:50%;
}

.cross::before,
.cross::after {
  width:5px;
  height:75px;
  background:hsl(300, 15%, 33%);
}

.playing .cross::before,
.playing .cross::after {
  background:hsl(194, 100%, 73%);
}

.cross::before {
  transform:translate(-50%, -50%) rotate(45deg);
}

.cross::after {
  transform:translate(-50%, -50%) rotate(-45deg);
}

.circle::after {
  width:70px;
  height:70px;
  border-radius:50%;
  transform:translate(-50%, -50%);
  border:5px solid hsl(300, 15%, 33%);
}

.playing .circle::after {
  border-color:hsl(7, 63%, 78%);
}

.playing .cell:not(.cross):not(.circle){
  cursor:pointer;
}

.playing .cell:not(.cross):not(.circle):hover{
  background:hsl(300, 15%, 34%);
}

#instructions {
  display: none;
}

.message {
  text-align: center;
  color: hsla(300, 15%, 20%, 1);
  font-size: 2rem;
}

.play-btn {

  position:absolute;
  top:10px;
  left:50%;
  outline:none;
  border:none;
  cursor:pointer;

  background: hsl(300, 3%, 18%);
  padding: 1rem 1.5rem;

  font-size: 2.4rem;
  color:hsla(300, 15%, 44%, 1);
  border-radius: 0 0 0.2rem 0.2rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  border:1px solid hsla(300, 3%, 17%, 1);
  transform:translate(-50%, 0);
  transition: transform 200ms ease-out;
}

.play-btn:hover {
  background: hsl(300, 3%, 20%);
}

.play-btn.hide {
  transform:translate(-50%, -100%);
}

header {
  max-width: 320px;
  margin:0 auto 20px;
}

.scores {
  display:flex;
  justify-content: space-between;
  position:relative;
  max-width: 320px;
  margin:2rem auto 0 ;
  border-top: 2px solid hsla(300, 15%, 20%, 1);
  padding-top: 2rem;
  opacity: 1;
  transform: translate(0, 0);
  transition: all 200ms 75ms ease-out;
}

.scores.hide {
  display: flex;
  opacity:0;
  transform: translate(0, 20%);
}

.scores div {
  flex:1;
}

.scores span {
  display:block;
  color:hsla(300, 15%, 20%, 1);
}

.scores ul {
  list-style: none;
  margin:0;
  padding: 0;
  display: inline-block;
}

.scores li {
  width: 10px;
  height:10px;
  border: 2px solid hsla(300, 15%, 20%, 1);
  border-radius:50%;
  display: inline-block;
}

.scores li.won {
  background: hsla(300, 15%, 53%, 1);
  animation: win 300ms;
}

@keyframes win {
  0% {transform: scale(1);}
  40% {transform: scale(3); }
  100% {transform: scale(1);}
}

.scores::after{
  display: none;
  content: 'vs';
  position: absolute;
  left:50%;
  top:50%;
  font-size: 2.4rem;
  transform:translate(-50%, -50%);
  color:#bdbdbd;
}

.scores >div:last-child{
  text-align: right;
}

.hide {  display:none;}

</style>
