<template>
  <div id="app">
    <AudioRecorder />
  </div>
</template>

<script>
import AudioRecorder from './components/AudioRecorder.vue';

export default {
  components: {
    AudioRecorder,
  },
};
</script>

<style scoped>
#app {
  margin-inline: 45px;
}
</style>