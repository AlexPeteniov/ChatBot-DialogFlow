<template>
  <main id="app">
    <!-- The input -->
    <div class="query">
        <input :aria-label="config.locale.strings.queryTitle" autocomplete="off" v-model="query" class="queryform" @keyup.enter="submit()" :placeholder="config.locale.strings.queryTitle" autofocus type="text">
    </div>
      <!-- Chat window -->
      <table v-for="a in answers" :key='a' class="chat-window">
        <!-- Your messages -->
        <tr>
          <td class="bubble">{{a.result.resolvedQuery}}</td>
        </tr>
        <!-- Dialogflow messages -->
        <tr>
          <td>
            <!-- Bot message types / Speech -->

            <div v-if="a.result.fulfillment.speech" class="bubble bot">
              {{a.result.fulfillment.speech}}
            </div>
            <!-- Google Assistant output -->
            <div v-for="r in a.result.fulfillment.messages" :key='r'>
            </div>
          </td>
        </tr>
      </table>
  </main>
</template>
<script>
import { ApiAiClient } from 'api-ai-javascript'
import config from './../config'
const client = new ApiAiClient({accessToken: config.app.token})
export default {
  name: 'app',
  data () {
    return {
      answers: [],
      query: '',
      config
    }
  },
  methods: {
    submit () {
      client.textRequest(this.query).then((response) => {
        if (response.result.action === 'input.unknown') {
          response.result.fulfillment.messages[0].unknown = true
          response.result.fulfillment.messages[0].text = response.result.resolvedQuery
        }
        this.answers.push(response)
        this.query = ''
      })
    }
  }}
</script>
<style >
  body{
    margin: 0;
    background:olivedrab url('../assets/patt.png') repeat;
    font-family: "Ultra", serif;
  }
  .query {
    padding: 16px 0px;
    background-color: white;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
    z-index: 999;
    position: fixed;
    width: 100%;
    margin-bottom: 30px;
    }

  .queryform {
    border: 0;
    width: 80%;
    margin-left: 60px;
    font-size: 16px;
    outline: none;
    color: rgba(0, 0, 0, 0.8);
    font-weight: 500;
  }
  @media screen and (max-width: 320px) {
    .queryform {
      width: 65%;
    }
  }
  .chat-window {
    width: 100%;
  }

  .bubble {
    max-width: 300px;
    background-color: #E1E1E1;
    padding: 16px;
    border-radius: 8px;
    color: rgba(0, 0, 0, 0.7);
    float: right;
    animation: msg 0.25s linear;
  }
  .bubble.bot {
    background-color: white;
    float: left;
    margin-right: 10px;
  }
  td {
    margin-top: 55px;
    margin-bottom:0px;
  }
</style>
