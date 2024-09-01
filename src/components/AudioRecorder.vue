<template>
    <div>
        <div class="audio-recorder">
            <div class="audio-recorder__container">
                <div class="audio-recorder__timer">
                    <span>{{ formatTime(currentTime) }}</span>
                </div>
                <div class="audio-recorder__controls">
                    <button v-if="!isRecording" class="audio-recorder__button" @click="startRecording"
                        :disabled="permissionDenied">
                        <svg width="50px" height="50px" viewBox="0 0 24 24" fill="none"
                            xmlns="http://www.w3.org/2000/svg">
                            <path fill-rule="evenodd" clip-rule="evenodd"
                                d="M6.25 8C6.25 4.82436 8.82436 2.25 12 2.25C15.1756 2.25 17.75 4.82436 17.75 8V11C17.75 14.1756 15.1756 16.75 12 16.75C8.82436 16.75 6.25 14.1756 6.25 11V8ZM7.81597 7.25H9C9.41421 7.25 9.75 7.58579 9.75 8C9.75 8.41421 9.41421 8.75 9 8.75H7.75V10.25H9C9.41421 10.25 9.75 10.5858 9.75 11C9.75 11.4142 9.41421 11.75 9 11.75H7.81597C8.1702 13.7395 9.9087 15.25 12 15.25C14.0913 15.25 15.8298 13.7395 16.184 11.75L13.5 11.75C13.0858 11.75 12.75 11.4142 12.75 11C12.75 10.5858 13.0858 10.25 13.5 10.25L16.25 10.25V8.75H13.5C13.0858 8.75 12.75 8.41421 12.75 8C12.75 7.58579 13.0858 7.25 13.5 7.25H16.184C15.8298 5.26049 14.0913 3.75 12 3.75C9.9087 3.75 8.1702 5.26049 7.81597 7.25ZM4 9.25C4.41421 9.25 4.75 9.58579 4.75 10V11C4.75 15.0041 7.99594 18.25 12 18.25C16.0041 18.25 19.25 15.0041 19.25 11V10C19.25 9.58579 19.5858 9.25 20 9.25C20.4142 9.25 20.75 9.58579 20.75 10V11C20.75 15.5798 17.2314 19.3379 12.75 19.7183V22C12.75 22.4142 12.4142 22.75 12 22.75C11.5858 22.75 11.25 22.4142 11.25 22V19.7183C6.7686 19.3379 3.25 15.5798 3.25 11V10C3.25 9.58579 3.58579 9.25 4 9.25Z"
                                fill="#1C274C" />
                        </svg>
                    </button>
                    <button v-if="isRecording && !isPaused" class="audio-recorder__button" @click="pauseRecording">
                        <svg width="50px" height="50px" viewBox="0 0 24 24" fill="none"
                            xmlns="http://www.w3.org/2000/svg">
                            <circle cx="12" cy="12" r="10" stroke="#1C274C" stroke-width="1.5" />
                            <path
                                d="M8 9.5C8 9.03406 8 8.80109 8.07612 8.61732C8.17761 8.37229 8.37229 8.17761 8.61732 8.07612C8.80109 8 9.03406 8 9.5 8C9.96594 8 10.1989 8 10.3827 8.07612C10.6277 8.17761 10.8224 8.37229 10.9239 8.61732C11 8.80109 11 9.03406 11 9.5V14.5C11 14.9659 11 15.1989 10.9239 15.3827C10.8224 15.6277 10.6277 15.8224 10.3827 15.9239C10.1989 16 9.96594 16 9.5 16C9.03406 16 8.80109 16 8.61732 15.9239C8.37229 15.8224 8.17761 15.6277 8.07612 15.3827C8 15.1989 8 14.9659 8 14.5V9.5Z"
                                stroke="#1C274C" stroke-width="1.5" />
                            <path
                                d="M13 9.5C13 9.03406 13 8.80109 13.0761 8.61732C13.1776 8.37229 13.3723 8.17761 13.6173 8.07612C13.8011 8 14.0341 8 14.5 8C14.9659 8 15.1989 8 15.3827 8.07612C15.6277 8.17761 15.8224 8.37229 15.9239 8.61732C16 8.80109 16 9.03406 16 9.5V14.5C16 14.9659 16 15.1989 15.9239 15.3827C15.8224 15.6277 15.6277 15.8224 15.3827 15.9239C15.1989 16 14.9659 16 14.5 16C14.0341 16 13.8011 16 13.6173 15.9239C13.3723 15.8224 13.1776 15.6277 13.0761 15.3827C13 15.1989 13 14.9659 13 14.5V9.5Z"
                                stroke="#1C274C" stroke-width="1.5" />
                        </svg>
                    </button>
                    <button v-if="isPaused" class="audio-recorder__button" @click="resumeRecording">
                        <svg width="50px" height="50px" viewBox="0 0 24 24" fill="none"
                            xmlns="http://www.w3.org/2000/svg">
                            <circle cx="12" cy="12" r="10" stroke="#1C274C" stroke-width="1.5" />
                            <path
                                d="M15.4137 10.941C16.1954 11.4026 16.1954 12.5974 15.4137 13.059L10.6935 15.8458C9.93371 16.2944 9 15.7105 9 14.7868L9 9.21316C9 8.28947 9.93371 7.70561 10.6935 8.15419L15.4137 10.941Z"
                                stroke="#1C274C" stroke-width="1.5" />
                        </svg>
                    </button>
                    <button v-if="isRecording" class="audio-recorder__button" @click="stopRecording">
                        <svg width="50px" height="50px" viewBox="0 0 24 24" fill="none"
                            xmlns="http://www.w3.org/2000/svg">
                            <circle cx="12" cy="12" r="10" stroke="#1C274C" stroke-width="1.5" />
                            <path
                                d="M8 12C8 10.1144 8 9.17157 8.58579 8.58579C9.17157 8 10.1144 8 12 8C13.8856 8 14.8284 8 15.4142 8.58579C16 9.17157 16 10.1144 16 12C16 13.8856 16 14.8284 15.4142 15.4142C14.8284 16 13.8856 16 12 16C10.1144 16 9.17157 16 8.58579 15.4142C8 14.8284 8 13.8856 8 12Z"
                                stroke="#1C274C" stroke-width="1.5" />
                        </svg>
                    </button>
                </div>
            </div>
            <p v-if="permissionDenied" class="audio-recorder__error">
                Permission to access microphone was denied. <br />
                Please allow access to use the audio recorder.
            </p>
        </div>

        <div v-if="savedAudios.length" class="table">
            <table class="table__saved-recordings">
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
                        <td class="table__saved">
                            <button @click="deleteAudio(index)">
                                <svg width="50px" height="50px" viewBox="0 0 24 24" fill="none"
                                    xmlns="http://www.w3.org/2000/svg">
                                    <path d="M10 11V17" stroke="#000000" stroke-width="2" stroke-linecap="round"
                                        stroke-linejoin="round" />
                                    <path d="M14 11V17" stroke="#000000" stroke-width="2" stroke-linecap="round"
                                        stroke-linejoin="round" />
                                    <path d="M4 7H20" stroke="#000000" stroke-width="2" stroke-linecap="round"
                                        stroke-linejoin="round" />
                                    <path d="M6 7H12H18V18C18 19.6569 16.6569 21 15 21H9C7.34315 21 6 19.6569 6 18V7Z"
                                        stroke="#000000" stroke-width="2" stroke-linecap="round"
                                        stroke-linejoin="round" />
                                    <path d="M9 5C9 3.89543 9.89543 3 11 3H13C14.1046 3 15 3.89543 15 5V7H9V5Z"
                                        stroke="#000000" stroke-width="2" stroke-linecap="round"
                                        stroke-linejoin="round" />
                                </svg>
                            </button>
                            <a :href="audio.url" :download="`audio_${index + 1}.wav`">
                                <button>
                                    <svg width="50px" height="50px" viewBox="0 0 24 24" fill="none"
                                        xmlns="http://www.w3.org/2000/svg">
                                        <path
                                            d="M17 17H17.01M17.4 14H18C18.9319 14 19.3978 14 19.7654 14.1522C20.2554 14.3552 20.6448 14.7446 20.8478 15.2346C21 15.6022 21 16.0681 21 17C21 17.9319 21 18.3978 20.8478 18.7654C20.6448 19.2554 20.2554 19.6448 19.7654 19.8478C19.3978 20 18.9319 20 18 20H6C5.06812 20 4.60218 20 4.23463 19.8478C3.74458 19.6448 3.35523 19.2554 3.15224 18.7654C3 18.3978 3 17.9319 3 17C3 16.0681 3 15.6022 3.15224 15.2346C3.35523 14.7446 3.74458 14.3552 4.23463 14.1522C4.60218 14 5.06812 14 6 14H6.6M12 15V4M12 15L9 12M12 15L15 12"
                                            stroke="#000000" stroke-width="2" stroke-linecap="round"
                                            stroke-linejoin="round" />
                                    </svg>
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
            permissionDenied: false,
            timerPaused: false,
            timerInterval: null
        };
    },
    methods: {
        async initializeMedia() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                this.mediaRecorder = new MediaRecorder(stream);
            } catch (error) {
                console.error("Error accessing media devices:", error);
                this.permissionDenied = true;
            }
        },
        async startRecording() {
            if (this.permissionDenied || !this.mediaRecorder) return;
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
                        this.initializeMedia();
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
            const hours = Math.floor(minutes / 60);
            if (hours > 0) {
                return `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}.${millisecondsInSeconds.toString().padStart(2, '0')}`;
            }
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
    },
    mounted() {
        this.initializeMedia();
    }
};
</script>

<style lang="scss" scoped>
.audio-recorder {
    &__container {
        background-color: #f9f9f9;
        padding: 20px;
        border-radius: 8px;
        display: flex;
        flex-direction: column;
        align-items: center;
        max-width: 300px;
        width: 100%;
        margin: 0 auto;
        font-family: 'Arial', sans-serif;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    }

    &__timer {
        font-size: 24px;
        font-weight: bold;
        color: #333;
        margin-bottom: 20px;
    }

    &__controls {
        display: flex;
        justify-content: center;
        width: 100%;
    }

    &__button {
        background: none;
        border: none;
        font-size: 24px;
        cursor: pointer;
        margin: 0 10px;
        transition: color 0.3s ease;

        &:hover {
            color: #0056b3;
        }
    }

    &__error {
        color: red;
        text-align: center;
        margin-top: 20px;
    }
}

.table {
    display: flex;
    flex-direction: column;
    align-items: center;
    max-width: 600px;
    width: 100%;
    margin: 0 auto;
    margin-top: 25px;
    font-family: 'Arial', sans-serif;

    &__saved-recordings {
        th {
            padding: 10px;
            text-align: left;
            background-color: #f2f2f2;
        }

        td {
            padding: 10px;
            text-align: left;
        }
    }

    &__saved {
        button {
            background: none;
            border: none;
            font-size: 24px;
            cursor: pointer;
            margin: 0 10px;
            transition: color 0.3s ease;
        }
    }
}
</style>