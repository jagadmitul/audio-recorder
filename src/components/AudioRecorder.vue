<template>
    <div>
    <div class="audio-recorder-container">
        <div class="timer-display">
            <span>{{ formatTime(currentTime) }}</span>
        </div>
        <div class="recorder">
            <button v-if="!isRecording" class="control-button" @click="startRecording">
                <i class="fas fa-microphone"></i>
            </button>
            <button v-if="isRecording && !isPaused" class="control-button" @click="pauseRecording">
                <i class="fas fa-circle-pause"></i>
            </button>
            <button v-if="isPaused" class="control-button" @click="resumeRecording">
                <i class="fas fa-play"></i>
            </button>
            <button v-if="isRecording" class="control-button" @click="stopRecording">
                <i class="fas fa-circle-stop"></i>
            </button>
        </div>
        </div>

        <div v-if="savedAudios.length" class="table">
            <table class="saved-recordings">
                <thead>
                    <tr>
                        <th>Audio File</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(audio, index) in savedAudios" :key="index">
                        <td>
                            <audio :src="audio.url" controls></audio>
                        </td>
                        <td class="saved">
                            <button @click="deleteAudio(index)">
                                <i class="fas fa-trash"></i>
                            </button>
                            <a :href="audio.url" :download="`audio_${index + 1}.wav`">
                                <button>
                                    <i class="fas fa-download"></i>
                                </button>
                            </a>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            mediaRecorder: null,
            chunks: [],
            audioBlob: null,
            audioUrl: null,
            audioFilename: 'recording.wav',
            savedAudios: [],
            currentTime: 0,
            startTime: null,
            isRecording: false,
            isPaused: false,
            timerPaused: false,
            timerInterval: null
        };
    },
    methods: {
        async startRecording() {
            const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
            this.mediaRecorder = new MediaRecorder(stream);
            this.mediaRecorder.start();
            this.isRecording = true;
            this.isPaused = false;
            this.startTimer();

            this.mediaRecorder.ondataavailable = (e) => {
                this.chunks.push(e.data);
            };
        },
        pauseRecording() {
            if (this.mediaRecorder && this.mediaRecorder.state === "recording") {
                this.mediaRecorder.pause();
                this.isPaused = true;
                this.pauseTimer();
            }
        },
        resumeRecording() {
            if (this.mediaRecorder && this.mediaRecorder.state === "paused") {
                this.mediaRecorder.resume();
                this.isPaused = false;
                this.resumeTimer();
            }
        },
        stopRecording() {
            if (this.mediaRecorder) {
                this.mediaRecorder.stop();
                this.isRecording = false;
                this.stopTimer();
                this.mediaRecorder.onstop = () => {
                    this.audioBlob = new Blob(this.chunks, { type: 'audio/wav' });
                    this.audioUrl = URL.createObjectURL(this.audioBlob);
                    this.audioFilename = `recording_${new Date().toISOString().slice(0, 19).replace(/:/g, '-')}.wav`;
                    this.chunks = [];
                    this.currentTime = 0;
                    this.isPaused = false;

                    if (this.audioBlob) {
                        const audio = {
                            url: this.audioUrl,
                            blob: this.audioBlob,
                            filename: this.audioFilename,
                        };
                        this.savedAudios.push(audio);
                        this.clearRecording();
                    }
                };
            }
        },
        clearRecording() {
            this.audioBlob = null;
            this.audioUrl = null;
        },
        deleteAudio(index) {
            this.savedAudios.splice(index, 1);
        },
        formatTime(milliseconds) {
            const totalSeconds = Math.floor(milliseconds / 1000);
            const minutes = Math.floor(totalSeconds / 60);
            const seconds = totalSeconds % 60;
            const millisecondsInSeconds = Math.floor((milliseconds % 1000) / 10);
            return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}.${millisecondsInSeconds.toString().padStart(2, '0')}`;
        },
        startTimer() {
            this.startTime = new Date().getTime();
            this.timerInterval = setInterval(() => {
                if (!this.isPaused) {
                    this.currentTime = new Date().getTime() - this.startTime;
                }
            }, 10);
        },
        pauseTimer() {
            this.timerPaused = true;
        },
        resumeTimer() {
            this.timerPaused = false;
            this.startTime = new Date().getTime() - this.currentTime;
        },
        stopTimer() {
            clearInterval(this.timerInterval);
            this.timerInterval = null;
        },
    }
};
</script>

<style scoped>
@import '@fortawesome/fontawesome-free/css/all.css';

.audio-recorder-container {
    background-color: #f9f9f9;
    padding: 20px;
    border-radius: 8px;
    display: flex;
    flex-direction: column;
    align-items: center;
    max-width: 800px;
    width: 100%;
    margin: 0 auto;
    font-family: 'Arial', sans-serif;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.table {
    display: flex;
    flex-direction: column;
    align-items: center;
    max-width: 840px;
    width: 100%;
    margin: 0 auto;
    margin-top: 25px;
    font-family: 'Arial', sans-serif;
}

.timer-display {
    font-size: 24px;
    font-weight: bold;
    color: #333;
    margin-bottom: 20px;
}

.recorder {
    display: flex;
    justify-content: center;
    width: 100%;
    margin-bottom: 20px;
}

.control-button {
    background: none;
    border: none;
    font-size: 24px;
    cursor: pointer;
    margin: 0 10px;
    transition: color 0.3s ease;
}

.control-button i {
    color: #007bff;
}

.control-button:hover i {
    color: #0056b3;
}

.saved-recordings {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
}

.saved-recordings th,
.saved-recordings td {
    padding: 10px;
    text-align: left;
}

.saved-recordings th {
    background-color: #f2f2f2;
}

.saved-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 10px;
}

.saved button {
    background-color: #007bff;
    border: none;
    color: white;
    padding: 10px 20px;
    text-align: center;
    font-size: 16px;
    margin: 0 5px;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s ease;
}

.saved button:hover {
    background-color: #0056b3;
}
</style>