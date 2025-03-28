<template>
  <div class="container" :style="{ marginTop: containerMargin }">
    <h2 class="sticky-title">ОФОРМЛЕНИЕ ПОДПИСКИ 🌸</h2>
    <DatePicker v-if="step === 'datePicker'" @datesSelected="handleDatesChosen" />
    <SendForm v-else-if="step === 'sendForm'" :selected-dates="selectedDates" @success="step = 'successMessage'" />
    <SuccessMessage v-else @reset="resetProcess" />
  </div>
</template>

<script>
import DatePicker from "@/components/DatePicker.vue";
import SendForm from "@/components/SendForm.vue";
import SuccessMessage from "@/components/SuccessMessage.vue";

export default {
  components: { DatePicker, SendForm, SuccessMessage },
  data() {
    return {
      step: "datePicker",
      selectedDates: [],
      containerMargin: "0px",
    };
  },
  methods: {
    handleDatesChosen(dates) {
      this.selectedDates = dates;
      this.step = "sendForm";
    },
    resetProcess() {
      this.selectedDates = [];
      this.step = "datePicker";
    },
    adjustForKeyboard() {
      const viewportHeight = window.visualViewport.height;
      const windowHeight = window.innerHeight;
      this.containerMargin = viewportHeight < windowHeight ? `-${windowHeight - viewportHeight}px` : "0px";
    },
  },
  mounted() {
    if (window.Telegram?.WebApp) {
      window.Telegram.WebApp.ready();
      setTimeout(() => window.Telegram.WebApp.expand(), 100);
    }
    window.visualViewport.addEventListener("resize", this.adjustForKeyboard);
  },
  beforeUnmount() {
    window.visualViewport.removeEventListener("resize", this.adjustForKeyboard);
  },
};
</script>



<style>
:root {
  color-scheme: light !important;
}

html, body, #app {
  background-color: #fff !important;
  color: #000 !important;
  min-height: 100vh;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  width: 100%;
  max-width: 390px;
  padding: 20px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: margin-top 0.3s ease-in-out;
}

h2 {
  font-size: 16px;
  font-weight: 500;
  text-transform: uppercase;
  margin-bottom: 25px;
  font-family: "SF Pro", sans-serif;
  text-align: center;
}

.sticky-title {
  position: sticky;
  top: 0;
  z-index: 10;
  background-color: white;
  padding: 15px 0;
  width: 100%;
  text-align: center;
  font-size: 16px;
  font-weight: 500;
  text-transform: uppercase;
  font-family: "SF Pro", sans-serif;
}

@media (min-width: 500px) {
  .container {
    max-width: 450px;
  }
}

@media (min-width: 768px) {
  .container {
    max-width: 600px;
  }
}

@media (min-width: 1024px) {
  .container {
    max-width: 800px;
  }
}
</style>
