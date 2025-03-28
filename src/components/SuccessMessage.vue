<template>
  <div class="success-container">
    <svg class="checkmark" viewBox="0 0 52 52">
      <path d="M14 27l8 8 16-16" fill="none" stroke-width="4" stroke-linecap="round" stroke-linejoin="round" />
    </svg>
    <p>ВЫ УСПЕШНО СОЗДАЛИ ПОДПИСКУ НА ЦВЕТЫ<br />СПАСИБО ЧТО ВЫБРАЛИ НАС</p>
    <button class="continue-btn" @click="closePage">Готово</button>
  </div>
</template>

<script>
export default {
  methods: {
    closePage() {
      if (window.Telegram && window.Telegram.WebApp) {
        window.Telegram.WebApp.close();
      } else {
        window.close();
      }
    }
  }
};
</script>

<style scoped>
.success-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  gap: 20px;
}

.checkmark {
  width: 50px;
  height: 50px;
  stroke: black;
  stroke-dasharray: 48;
  stroke-dashoffset: 48;
  animation: check 0.5s ease forwards;
}

@keyframes check {
  to {
    stroke-dashoffset: 0;
  }
}

p {
  font-family: "SF Pro", sans-serif;
  font-size: 14px;
  color: black;
  line-height: 1.4;
}

.continue-btn {
  margin-top: 50px;
  padding: 10px;
  border: 1px solid black;
  background: transparent;
  font-weight: 500;
  text-transform: uppercase;
  cursor: pointer;
  width: 100%;
  color: black;
  box-sizing: border-box;
}
</style>
