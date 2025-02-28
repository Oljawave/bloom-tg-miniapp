<template>
    <div class="form-container">
      <div class="input-group">
        <label :class="{ active: formattedDates, label: true }">ВЫБРАННЫЕ ДАТЫ</label>
        <input type="text" :value="formattedDates" readonly />
      </div>
      
      <div class="input-group">
        <label :class="{ active: selectedPrice, label: true }">ВЫБЕРИТЕ ЦЕНОВОЙ ДИАПАЗОН</label>
        <select v-model="selectedPrice" required :class="{ 'error-border': errorFields.selectedPrice }">
          <option disabled value=""></option>
          <option>5000 ₸ - 10000 ₸</option>
          <option>10000 ₸ - 15000 ₸</option>
          <option>15000 ₸ - 20000 ₸</option>
          <option>20000 ₸ +</option>
        </select>
        <p v-if="errorFields.selectedPrice" class="error-message">Пожалуйста, выберите ценовой диапазон</p>
      </div>
      
      <div class="input-group">
        <label :class="{ active: selectedCity, label: true }">ВЫБЕРИТЕ ГОРОД</label>
        <select v-model="selectedCity" required :class="{ 'error-border': errorFields.selectedCity }">
          <option disabled value=""></option>
          <option v-for="city in cities" :key="city">{{ city }}</option>
        </select>
        <p v-if="errorFields.selectedCity" class="error-message">Пожалуйста, выберите город</p>
      </div>
      
      <div class="input-group">
        <label :class="{ active: street, label: true }">УЛИЦА</label>
        <input v-model="street" type="text" :class="{ 'error-border': errorFields.street }" />
        <p v-if="errorFields.street" class="error-message">Пожалуйста, введите улицу</p>
      </div>
      
      <div class="row">
        <div class="input-group">
          <label :class="{ active: building, label: true }">ДОМ/ЗДАНИЕ</label>
          <input v-model="building" type="number" @input="validateNumber('building')" :class="{ 'error-border': errorFields.building }" />
          <p v-if="errorFields.building" class="error-message">Пожалуйста, введите дом/здание</p>
        </div>
        <div class="input-group">
          <label :class="{ active: apartment, label: true }">КВАРТИРА/ОФИС</label>
          <input v-model="apartment" type="number" @input="validateNumber('apartment')" :class="{ 'error-border': errorFields.apartment }" />
          <p v-if="errorFields.apartment" class="error-message">Пожалуйста, введите квартиру/офис</p>
        </div>
      </div>
  
      <div class="row">
        <div class="input-group">
          <label :class="{ active: entrance, label: true }">ПОДЪЕЗД</label>
          <input v-model="entrance" type="number" @input="validateNumber('entrance')" :class="{ 'error-border': errorFields.entrance }" />
          <p v-if="errorFields.entrance" class="error-message">Пожалуйста, введите подъезд</p>
        </div>
        <div class="input-group">
          <label :class="{ active: floor, label: true }">ЭТАЖ</label>
          <input v-model="floor" type="number" @input="validateNumber('floor')" :class="{ 'error-border': errorFields.floor }" />
          <p v-if="errorFields.floor" class="error-message">Пожалуйста, введите этаж</p>
        </div>
      </div>
  
      <div class="input-group">
        <label :class="{ active: phone, label: true }">ВВЕДИТЕ НОМЕР ТЕЛЕФОНА</label>
        <input v-model="phone" type="tel" @input="formatPhone" :class="{ 'error-border': errorFields.phone }" />
        <p v-if="errorFields.phone" class="error-message">Пожалуйста, введите номер телефона</p>
      </div>
      
      <div class="input-group">
        <label :class="{ active: comment, label: true }">КОММЕНТАРИЙ К ДОСТАВКЕ</label>
        <input v-model="comment" type="text" />
      </div>
      
      <button @click="submitForm">ПОДТВЕРДИТЬ</button>
    </div>
  </template>
  
  
  <script>
  export default {
    props: {
      selectedDates: Array
    },
    computed: {
      formattedDates() {
        return this.selectedDates.map(date => {
          const [year, month, day] = date.split("-");
          return `${day}.${month}`;
        }).join(", ");
      }
    },
    data() {
      return {
        selectedPrice: "",
        selectedCity: "",
        street: "",
        building: "",
        apartment: "",
        entrance: "",
        floor: "",
        phone: "",
        comment: "",
        cities: [
          "Караганда", "Алматы", "Астана", "Шымкент", "Актобе", "Тараз",
          "Павлодар", "Оскемен", "Семей", "Атырау", "Костанай",
          "Кызылорда", "Орал", "Петропавловск", "Актау", "Темиртау",
          "Туркестан", "Кокшетау", "Талдыкорган"
        ],
        errorFields: {
          selectedPrice: false,
          selectedCity: false,
          street: false,
          building: false,
          apartment: false,
          entrance: false,
          floor: false,
          phone: false
        }
      };
    },
    methods: {
      validateNumber(field) {
        this[field] = this[field].replace(/\D/g, "");
      },
      formatPhone() {
        let value = this.phone.replace(/\D/g, "").substring(0, 11);
        if (!value.startsWith("7")) value = "7" + value;
  
        this.phone = `+7 (${value.substring(1, 4)}) ${value.substring(4, 7)}-${value.substring(7, 9)}-${value.substring(9, 11)}`.trim();
      },
      submitForm() {
        this.errorFields.selectedPrice = !this.selectedPrice;
        this.errorFields.selectedCity = !this.selectedCity;
        this.errorFields.phone = !this.phone || this.phone.length !== 18;
        this.errorFields.apartment = !this.apartment;
        this.errorFields.building = !this.building;
        this.errorFields.street = !this.street;
        this.errorFields.entrance = !this.entrance;
        this.errorFields.floor = !this.floor;
  
        if (Object.values(this.errorFields).some(error => error)) return;
  
        const formattedPhone = this.phone.replace(/[^+0-9]/g, "");
        console.log("Отправка формы с номером:", formattedPhone);
      }
    },
    mounted() {
      const inputs = this.$el.querySelectorAll("input, textarea");
      
      inputs.forEach(input => {
        input.addEventListener("focus", function () {
          setTimeout(() => {
            this.scrollIntoView({ behavior: "smooth", block: "center" });
          }, 300);
        });
  
        input.addEventListener("blur", function () {
          window.scrollTo({ top: 0, behavior: "smooth" });
        });
      });
    }
  };
  </script>
  

  
    
    <style scoped>
  
    body {
      font-family: 'SF Pro', sans-serif;
    }
  
    .form-container {
      width: 100%;
      max-width: 390px;
      padding: 20px;
      box-sizing: border-box;
    }
  
    .input-group {
    position: relative;
    margin-top: 25px;
    width: 100%;
  }
  
  .label {
    font-family: 'SF Pro', sans-serif;
    position: absolute;
    left: 0;
    top: 8px; 
    font-size: 14px;
    color: #888;
    pointer-events: none;
    transition: all 0.2s ease;
  }
  
  .label.active {
    top: -14px;
    font-size: 12px;
    color: #000;
  }
  
  input, select {
    width: 100%;
    padding: 12px 0;
    border: none;
    border-bottom: 1px solid #000;
    font-size: 14px;
    background-color: transparent;
  }
  input:focus, select:focus {
    outline: none;
    border-bottom: 1px solid #000;
  }
  .error-message {
    font-size: 12px;
    color: #ff4d4f;
    margin-top: 5px;
    font-family: 'SF Pro', sans-serif;
  }
    .row {
      display: flex;
      gap: 10px;
    }
    .row input {
      flex: 1;
    }
    button {
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
    