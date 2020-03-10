<template>
  <div>
    <div v-if="error" class="badnewsbears">{{errorMessage}}</div>
    <div v-else>
      <h1 class="blinky">Messages</h1>
      <div v-for="message in msgs">{{message}}</div>
    </div>
    <input placeholder="enter a message" v-model="newText"></input><button v-on:click="submit">Submit!</button><button v-on:click="getMessages">Reload!</button>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import Axios, { AxiosInstance, AxiosRequestConfig, AxiosError, AxiosResponse, AxiosStatic } from 'axios';

interface IApiResponse {
  Messages: string[];
  ResponseCode: number;
  Message: string;
};

@Component
export default class HelloWorld extends Vue {
  private static _url: string = 'https://localhost:5000/api/S3Proxy/';
  private static _axios = Axios.create();
  @Prop() private msg!: string;
  public error: boolean = false;
  public errorMessage: string = '';
  public msgs: string[] = [];
  public newText: string = '';
  mounted() {
    this.getMessages();
  }
  async submit() {
    if(!this.newText) {
      alert('enter something!');
      return;
    }
    // not the prettiest way to do this...
    let response = await HelloWorld._axios.put<IApiResponse>(HelloWorld._url + encodeURI(this.newText));
    if(response.data.ResponseCode) {
      this.error = true;
      this.errorMessage = response.data.Message;
    }
    else {
      await this.getMessages();
      if(!this.error) {
        this.newText = '';
      }
    }
  }
  async getMessages() {
    let response = await HelloWorld._axios.get<IApiResponse>(HelloWorld._url);

    if(response.data.ResponseCode) {
      this.error = true;
      this.errorMessage = response.data.Message;
    }
    else {
      this.error = false;
      this.errorMessage = '';
      this.msgs = response.data.Messages;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.blinky{
    animation:blinky 1.2s infinite;
}
@keyframes blinky{
    0%{     color: #000;    }
    49%{    color: #000; }
    60%{    color: transparent; }
    99%{    color:transparent;  }
    100%{   color: #000;    }
}
.badnewsbears {
  border: 1px solid red;
  color: red;
  background-color: #ccf;
  padding: 10px;
}
</style>
