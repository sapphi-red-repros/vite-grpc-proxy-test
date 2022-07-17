<template>
  <div class="home">
    <h1>Demo grpc-web with Vite</h1>
    <br />
    <div>Enter your message: <input v-model="msg" /></div>
    <div>
      <button class="button" @click="getReply">
        Send Messages to GRPC Server
      </button>
    </div>
    <div class="response">
      Response from Server:
      <li v-for="(m, i) in msgs" :key="i">
        {{ m }}
      </li>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { EchoRequest } from './proto/echo'
import { EchoServiceClient } from './proto/echo.client'
import { GrpcWebFetchTransport } from '@protobuf-ts/grpcweb-transport'

const client = new EchoServiceClient(
  new GrpcWebFetchTransport({
    baseUrl: location.origin + '/api'
  })
)

const msg = ref('')
const msgs = ref([])

const getReply = () => {
  let request = EchoRequest.fromJson({
    query: msg.value
  })

  client.echo(request).then((res) => {
    let { response } = res
    console.log(response.reply)
    msgs.value.push(response.reply)
  })
}
</script>

<style lang="scss" scoped>
.home {
  font-size: 24px;
  font-family: 'Fira Sans';
  text-align: center;

  input {
    height: 36px;
    width: calc(100% - 32px);
    font-size: 24px;
    font-family: 'Fira Sans';
    border-radius: 8px;
    margin: 16px;
  }

  button {
    font-size: 24px;
    font-family: 'Fira Sans';
  }

  .response {
    text-align: left;
    margin: 16px;
  }
}
</style>
