<script lang="ts">
  import * as mqtt from "mqtt/dist/mqtt.min";
  import {onDestroy} from "svelte";

  const baseUrl = "wss://test.mosquitto.org:8081";
  const powerTopic = "device/power";

  let value: string = "0";

  const client = mqtt.connect(baseUrl);
  client.subscribe(powerTopic, handleError);

  client.on('message', function (topic: string, message: Uint8Array) {
    if(powerTopic === topic) {
      value = message.toString();
    };
  });

  function changeHandler(event: Event){
    const message = (event.target as HTMLInputElement).value;
    client.publish(powerTopic, message, handleError);
  }

  onDestroy(() => {
    client.end();
  });

  function handleError (error: Error | null) {
    if(error){
      console.log(error);
    };
  };
</script>

<div>
  <h1 class="title">Мощность</h1>
  <div class="value">
    {value}%
  </div>

  <input type="range" bind:value={value} on:change={changeHandler}/>
</div>
<style>
  .title {
    font-size: 18px;
  }
  .value {
    font-size: 24px;
  }
</style>