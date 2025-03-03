<template>
    <div class="datepicker-container">
      <div class="calendar-header">
        <Icon icon="lets-icons:expand-left-light" class="arrow left" @click="prevMonth" />
        <span class="month">{{ currentMonth.toUpperCase() }}</span>
        <Icon icon="lets-icons:expand-right-light" class="arrow right" @click="nextMonth" />
      </div>
      <hr :class="{ error: error }" />
      <div class="calendar">
        <div class="weekdays">
          <span v-for="day in weekdays" :key="day">{{ day }}</span>
        </div>
        <div class="days">
          <span v-for="(day, index) in days" :key="index" 
            :class="{ selected: selectedDates.includes(day?.date), disabled: day?.isPast }"
            @click="!day?.isPast ? toggleDate(day.date) : null">
            {{ day?.day || '' }}
          </span>
        </div>
      </div>
      <p v-if="error" class="error-text">Выберите хотя бы одну дату</p>
      <button class="continue-btn" @click="confirmDates">ПРОДОЛЖИТЬ</button>
    </div>
  </template>
  
  <script>
import { Icon } from '@iconify/vue';
import dayjs from 'dayjs';
import 'dayjs/locale/ru';

dayjs.locale('ru');

export default {
  components: { Icon },
  data() {
    return {
      currentDate: dayjs(),
      weekdays: ['ПН', 'ВТ', 'СР', 'ЧТ', 'ПТ', 'СБ', 'ВС'],
      selectedDates: [],
      error: false
    };
  },
  computed: {
    currentMonth() {
      return this.currentDate.format('MMMM');
    },
    days() {
      const startOfMonth = this.currentDate.startOf('month');
      const endOfMonth = this.currentDate.endOf('month');
      const today = dayjs().format('YYYY-MM-DD');
      const startDay = (startOfMonth.day() + 6) % 7;
      const totalDays = endOfMonth.date();

      return [
        ...Array(startDay).fill(null),
        ...[...Array(totalDays)].map((_, i) => {
          const date = this.currentDate.format(`YYYY-MM-${String(i + 1).padStart(2, '0')}`);
          return {
            day: i + 1,
            date,
            isPast: date < today 
          };
        })
      ];
    }
  },
  methods: {
    toggleDate(date) {
      if (this.selectedDates.includes(date)) {
        this.selectedDates = this.selectedDates.filter(d => d !== date);
      } else {
        this.selectedDates.push(date);
      }
      if (this.selectedDates.length > 0) {
        this.error = false;
      }
    },
    confirmDates() {
      if (this.selectedDates.length === 0) {
        this.error = true;
      } else {
        console.log('Выбранные даты:', this.selectedDates);
        this.$emit('datesSelected', this.selectedDates);
      }
    },
    prevMonth() {
      this.currentDate = this.currentDate.subtract(1, 'month');
    },
    nextMonth() {
      this.currentDate = this.currentDate.add(1, 'month');
    },
    adjustForKeyboard() {
      if (window.visualViewport.height < window.innerHeight) {
        document.body.style.paddingBottom = (window.innerHeight - window.visualViewport.height) + "px";
      } else {
        document.body.style.paddingBottom = "0px";
      }
    }
  },
  mounted() {
    if (window.Telegram && window.Telegram.WebApp) {
      window.Telegram.WebApp.ready();
      setTimeout(() => {
        window.Telegram.WebApp.expand();
      }, 100);
    }
    window.visualViewport.addEventListener("resize", this.adjustForKeyboard);
  },
  beforeUnmount() {
    window.visualViewport.removeEventListener("resize", this.adjustForKeyboard);
  }
};
</script>

  
  <style scoped>
  .datepicker-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: 'SF Pro', sans-serif;
    width: 100%;
    max-width: 350px;
    padding: 20px;
    box-sizing: border-box;
    background-color: #fff;
  }
  
  h2 {
    font-size: 16px;
    font-weight: 500;
    text-transform: uppercase;
    margin-bottom: 15px;
  }
  
  .calendar-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    margin-bottom: 10px;
  }
  
  .calendar-header .month {
    font-size: 14px;
    font-weight: bold;
  }
  
  .calendar-header .arrow {
    cursor: pointer;
    font-size: 20px;
  }
  
  hr {
    width: 100%;
    border: none;
    border-top: 1px solid black;
    margin-bottom: 10px;
    transition: border-color 0.3s;
  }
  
  hr.error {
    border-color: red;
  }
  
  .weekdays, .days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    width: 100%;
    text-align: center;
    gap: 8px;
  }
  
  .weekdays span {
    font-size: 12px;
    font-weight: bold;
    margin-bottom: 10px;
  }
  
  .days span {
    font-size: 14px;
    cursor: pointer;
    padding: 12px;
    border-radius: 50%;
    aspect-ratio: 1 / 1;
    transition: background 0.3s;
  }
  
  .days span.selected {
    background: black;
    color: white;
  }
  
  .days span.disabled {
    color: grey;
    pointer-events: none;
  }
  
  .days span.empty {
    visibility: hidden;
  }
  
  .error-text {
    color: red;
    font-size: 12px;
    margin-top: 5px;
    width: 100%;
    text-align: left;
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
  }
  </style>
  