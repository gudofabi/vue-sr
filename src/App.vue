<template>
  <div>
    <button class="microphone" @click="toggleMic">{{ isRecording ? 'Recording...' : 'Record' }}</button>
    <div class="transcript" v-text="transcript">

    </div>
  </div>
</template>


<script setup>
import { ref } from "@vue/reactivity";
import { onMounted } from "@vue/runtime-core";


const transcript = ref('');
const isRecording =  ref(false);

const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition;
const sr = new Recognition();

onMounted(() => {
  sr.continuous = true;
  sr.interimResults = true;

  sr.onstart = () => {
    console.log('SR Started');
    isRecording.value = true;
  }

  sr.onend = () => {
    console.log('SR Stopped');
    isRecording.value = false;
  }

  sr.onresult = (evt) => {

    for(let result of evt.results) {
      if(result.isFinal) checkForCommand(result)
    }

    // console.log(evt);
    const t = Array.from(evt.results)
      .map(result => result[0])
      .map(result => result.transcript)
      .join('');

    transcript.value = t;
  }
})

// check for words
const checkForCommand = (result) => {
  const t = result[0].transcript
  if(t.includes('stop recording'))
  {
    sr.stop();
  } else if(t.includes('what time is it?') || t.includes(`what time is it?`))
  {
    sr.stop()
    alert(new Date().toLocaleDateString());
    setTimeout(() => sr.start(), 100)
  }
}

const toggleMic = () => {
  if(isRecording.value) {
    sr.stop()
  }else {
    sr.start()
  }
}

</script>

<style scoped>

</style>
