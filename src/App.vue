<template>
  <div class="container" :style="{ marginTop: containerMargin }">
    <div v-if="step !== 'successMessage' && !progressBarHidden" class="progress-bar-container">
      <div class="progress-bar">
        <div class="progress" :style="{ width: progressWidth }"></div>
      </div>
      <div class="progress-labels">
        <span :class="{ active: step === 'datePicker' }">ВЫБОР ДАТЫ</span>
        <span :class="{ active: step === 'sendForm' }">ДЕТАЛИ ПОДПИСКИ</span>
        <span :class="{ active: step === 'flowerSelection' }">ВЫБОР БУКЕТА</span>
      </div>
    </div>

    <h2 class="sticky-title">
      <Icon
        v-if="step !== 'datePicker' && step !== 'successMessage'"
        icon="lets-icons:expand-left-light"
        class="back-arrow"
        @click="goToPreviousStep"
      />
      ОФОРМЛЕНИЕ ПОДПИСКИ
    </h2>

    <DatePicker v-if="step === 'datePicker'" @datesSelected="handleDatesChosen" />

    <SendForm
      v-else-if="step === 'sendForm'"
      :selected-dates="selectedDates"
      @nextStep="goToFlowerSelection"
      @skipFlowerSelection="goToFinalStep"
      @success="step = 'successMessage'"
    />

    <FlowerSelection
      v-else-if="step === 'flowerSelection'"
      @selectionConfirmed="handleSelectionConfirmed"
    />

    <SuccessMessage v-else @reset="resetProcess" />
  </div>
</template>

<script>
import axios from "axios";
import { Icon } from "@iconify/vue"; // ✅ Импорт Icon компонента
import DatePicker from "@/components/DatePicker.vue";
import SendForm from "@/components/SendForm.vue";
import SuccessMessage from "@/components/SuccessMessage.vue";
import FlowerSelection from "@/components/FlowerSelection.vue";

export default {
  components: {
    DatePicker,
    SendForm,
    SuccessMessage,
    FlowerSelection,
    Icon, // ✅ Регистрация компонента
  },
  data() {
    return {
      step: "datePicker",
      selectedDates: [],
      formData: {},
      containerMargin: "0px",
      progressBarHidden: false,
    };
  },
  computed: {
    progressWidth() {
      const steps = ["datePicker", "sendForm", "flowerSelection", "successMessage"];
      const index = steps.indexOf(this.step);
      return `${(index / (steps.length - 1)) * 100}%`;
    },
  },
  methods: {
    handleDatesChosen(dates) {
      this.selectedDates = dates;
      this.step = "sendForm";
    },

    goToFlowerSelection(formData) {
      this.formData = formData;
      localStorage.setItem("formData", JSON.stringify(formData));
      this.step = "flowerSelection";
    },

    async goToFinalStep(formData) {
      this.formData = formData;
      localStorage.setItem("formData", JSON.stringify(formData));
      this.progressBarHidden = true;

      try {
        const finalData = JSON.parse(localStorage.getItem("formData")) || {};
        const response = await axios.post("https://api.bloooom.kz/orders", finalData);
        console.log("Заказ успешно отправлен (без цветов):", response.data);
        this.step = "successMessage";
        localStorage.removeItem("formData");
      } catch (error) {
        console.error("Ошибка при отправке заказа (без цветов):", error);
      }
    },

    handleSelectionConfirmed() {
      setTimeout(() => {
        this.progressBarHidden = true;
        this.step = "successMessage";
      }, 500);
    },

    resetProcess() {
      this.selectedDates = [];
      this.formData = {};
      this.step = "datePicker";
      this.progressBarHidden = false;
    },

    adjustForKeyboard() {
      const viewportHeight = window.visualViewport.height;
      const windowHeight = window.innerHeight;
      this.containerMargin = viewportHeight < windowHeight ? `-${windowHeight - viewportHeight}px` : "0px";
    },

    goToPreviousStep() {
      if (this.step === "sendForm") {
        this.step = "datePicker";
      } else if (this.step === "flowerSelection") {
        this.step = "sendForm";
      }
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

.progress-bar-container {
  width: 100%;
  text-align: center;
  margin-bottom: 15px;
}

.progress-bar {
  width: 100%;
  height: 6px;
  border: 0.5px solid black;
  position: relative;
}

.progress {
  height: 100%;
  background: black;
  width: 0;
  transition: width 0.3s ease-in-out;
}

.progress-labels {
  display: flex;
  justify-content: space-between;
  font-size: 11px;
  margin-top: 5px;
  color: #666;
  font-family: "SF Pro", sans-serif;
}

.progress-labels .active {
  font-weight: bold;
  color: black;
}

.progress-bar-container {
  transition: opacity 0.5s ease, height 0.5s ease;
}

.progress-bar-container.hidden {
  opacity: 0;
  height: 0;
  overflow: hidden;
}

.progress {
  transition: width 0.3s ease;
}

.back-arrow {
  position: absolute;
  left: 15px;
  top: 50%;
  transform: translateY(-50%);
  font-size: 20px;
  cursor: pointer;
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
