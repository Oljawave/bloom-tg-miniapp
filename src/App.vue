<template>
  <div class="container" :style="{ marginTop: containerMargin }">
    <h2>ОФОРМЛЕНИЕ ПОДПИСКИ 🌸</h2>
    <DatePicker v-if="!datesSelected" @datesSelected="handleDatesChosen" />
    
    <div v-else class="input-group" @click="resetDateSelection">
      <label :class="{ active: formattedDates, label: true }">ВЫБРАННЫЕ ДАТЫ</label>
      <input type="text" :value="formattedDates" readonly />
    </div>

    <SendForm v-if="datesSelected" :selected-dates="selectedDates" />
  </div>
</template>

<script>
import DatePicker from "@/components/DatePicker.vue";
import SendForm from "@/components/SendForm.vue";

export default {
  components: {
    DatePicker,
    SendForm,
  },
  data() {
    return {
      datesSelected: false,
      selectedDates: [],
      containerMargin: "0px",
    };
  },
  computed: {
    formattedDates() {
      return this.selectedDates.length ? this.selectedDates.join(", ") : "";
    },
  },
  methods: {
    handleDatesChosen(dates) {
      this.selectedDates = dates;
      this.datesSelected = true;
    },
    resetDateSelection() {
      this.datesSelected = false;
    },
    adjustForKeyboard() {
      const viewportHeight = window.visualViewport.height;
      const windowHeight = window.innerHeight;

      if (viewportHeight < windowHeight) {
        this.containerMargin = `-${windowHeight - viewportHeight}px`;
      } else {
        this.containerMargin = "0px";
      }
    },
  },
  mounted() {
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

.input-group {
  width: 100%;
  display: flex;
  flex-direction: column;
  margin-top: 15px;
  cursor: pointer;
}

.input-group label {
  font-size: 12px;
  font-weight: 500;
  text-transform: uppercase;
  margin-bottom: 5px;
  font-family: "SF Pro", sans-serif;
}

.input-group input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  font-size: 14px;
  text-align: center;
  cursor: pointer;
  background-color: #fff;
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
