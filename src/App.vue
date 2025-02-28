<template>
  <div class="container">
    <h2>ОФОРМЛЕНИЕ ПОДПИСКИ 🌸</h2>
    <DatePicker v-if="!datesSelected" @datesSelected="handleDatesChosen" />
    <SendForm v-else :selected-dates="selectedDates" />
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
    };
  },
  methods: {
    handleDatesChosen(dates) {
      this.selectedDates = dates;
      this.datesSelected = true;
    },
  },
};
</script>

<style scoped>
body {
  font-family: 'SF Pro', sans-serif;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #fff !important;
}

html {
  background-color: #fff !important;
}

.container {
  width: 100%;
  max-width: 390px;
  padding: 20px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}

h2 {
  font-size: 16px;
  font-weight: 500;
  text-transform: uppercase;
  margin-bottom: 25px;
  font-family: 'SF Pro', sans-serif;
  align-self: flex-start;
  margin-left: 20px;
}

:root {
  background-color: #fff !important;
}

html, body {
  background-color: #fff !important;
}

@media (prefers-color-scheme: dark) {
    body {
        background-color: #000;
    }
}

#app {
    background-color: #fff !important;
}


</style>