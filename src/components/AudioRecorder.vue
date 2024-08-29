<template>
    <div>
        <h2>Audio Recorder</h2>
        <div class="controls">
            <button @click="startRecording" :disabled="isRecording">
                Start Recording
            </button>
            <button @click="pauseRecording" :disabled="!isRecording || isPaused">
                Pause Recording
            </button>
            <button @click="resumeRecording" :disabled="!isPaused">
                Resume Recording
            </button>
            <button @click="stopRecording" :disabled="!isRecording">
                Stop Recording
            </button>
            <button @click="clearRecording" :disabled="!audioUrl">
                Delete Recording
            </button>
        </div>

        <div v-if="audioUrl">
            <h3>Recorded Audio</h3>
            <div class="recorded-container">
                <audio :src="audioUrl" controls></audio>
                <div class="recorded">
                    <button @click="saveRecording">Save</button>
                    <a :href="audioUrl" :download="audioFilename" v-if="audioBlob">
                        <button>Download</button>
                    </a>
                </div>
            </div>
        </div>

        <div v-if="savedAudios.length">
            <h3>Saved Audios</h3>
            <div v-for="(audio, index) in savedAudios" :key="index" class="saved-container">
                <audio :src="audio.url" controls></audio>
                <div class="saved">
                    <button @click="deleteAudio(index)">Delete</button>
                    <a :href="audio.url" :download="`audio_${index + 1}.wav`">
                        <button>Download</button>
                    </a>
                </div>
            </div>
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
            isRecording: false,
            isPaused: false,
        };
    },
    methods: {
        async startRecording() {
            const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
            this.mediaRecorder = new MediaRecorder(stream);
            this.mediaRecorder.start();
            this.isRecording = true;
            this.isPaused = false;

            this.mediaRecorder.ondataavailable = (e) => {
                this.chunks.push(e.data);
            };
        },
        pauseRecording() {
            if (this.mediaRecorder && this.mediaRecorder.state === "recording") {
                this.mediaRecorder.pause();
                this.isPaused = true;
            }
        },
        resumeRecording() {
            if (this.mediaRecorder && this.mediaRecorder.state === "paused") {
                this.mediaRecorder.resume();
                this.isPaused = false;
            }
        },
        stopRecording() {
            if (this.mediaRecorder) {
                this.mediaRecorder.stop();
                this.isRecording = false;
                this.mediaRecorder.onstop = () => {
                    this.audioBlob = new Blob(this.chunks, { type: 'audio/wav' });
                    this.audioUrl = URL.createObjectURL(this.audioBlob);
                    this.audioFilename = `recording_${new Date().toISOString().slice(0, 19).replace(/:/g, '-')}.wav`;
                    this.chunks = [];
                };
            }
        },
        clearRecording() {
            this.audioBlob = null;
            this.audioUrl = null;
        },
        saveRecording() {
            if (this.audioBlob) {
                const audio = {
                    url: this.audioUrl,
                    blob: this.audioBlob,
                    filename: this.audioFilename,
                };
                this.savedAudios.push(audio);
                this.clearRecording();
            }
        },
        deleteAudio(index) {
            this.savedAudios.splice(index, 1);
        }
    }
};
</script>

<style scoped>
.controls {
    display: flex;
    justify-content: center;
    margin-top: 20px;
}

.controls button {
    background-color: #4caf50;
    border: none;
    color: white;
    padding: 10px 20px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
    margin: 4px 10px;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s ease;
}

.controls button:hover {
    background-color: #45a049;
}

.controls button:disabled {
    background-color: #cccccc;
    color: #666666;
    cursor: not-allowed;
}

.recorded-container,
.saved-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 10px;
}

.recorded,
.saved {
    display: flex;
    margin-left: 20px;
}

.recorded button,
.saved button {
    background-color: #007bff;
    border: none;
    color: white;
    padding: 10px 20px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
    margin: 0 5px;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s ease;
}

.recorded button:hover,
.saved button:hover {
    background-color: #0056b3;
}

.recorded a,
.saved a {
    text-decoration: none;
}

.recorded button:disabled,
.saved button:disabled {
    background-color: #cccccc;
    color: #666666;
    cursor: not-allowed;
}
</style>