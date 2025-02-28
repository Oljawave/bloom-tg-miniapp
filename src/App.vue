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

<style>
/* Отключаем автоматическое изменение темы */
:root {
    color-scheme: light !important;
}

/* Фиксируем белый фон на всю страницу */
html, body, #app {
    background-color: #fff !important;
    color: #000 !important; /* Чтобы текст не был белым */
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
}

h2 {
    font-size: 16px;
    font-weight: 500;
    text-transform: uppercase;
    margin-bottom: 25px;
    font-family: 'SF Pro', sans-serif;
    text-align: center;
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

