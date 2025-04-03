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
        <input v-model="building" type="text" @input="validateNumber('building')" :class="{ 'error-border': errorFields.building }" />
        <p v-if="errorFields.building" class="error-message">Пожалуйста, введите дом</p>
      </div>
      <div class="input-group">
        <label :class="{ active: apartment, label: true }">КВАРТИРА/ОФИС</label>
        <input v-model="apartment" type="text" inputmode="decimal" pattern="[0-9]*" @input="validateNumber('apartment')" />
      </div>
    </div>

    <div class="row">
      <div class="input-group">
        <label :class="{ active: entrance, label: true }">ПОДЪЕЗД</label>
        <input v-model="entrance" type="text" inputmode="decimal" pattern="[0-9]*" @input="validateNumber('entrance')" />
      </div>
      <div class="input-group">
        <label :class="{ active: floor, label: true }">ЭТАЖ</label>
        <input v-model="floor" type="text" inputmode="decimal" pattern="[0-9]*" @input="validateNumber('floor')" />
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
    
    <button @click="submitForm" :disabled="isSubmitting" class="confirm-btn">
      {{ isSubmitting ? "ОТПРАВКА..." : "ПОДТВЕРДИТЬ" }}
    </button>

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
      userId: null,
      cities: [
        "Астана",
        // "Караганда", "Алматы", "Шымкент", "Актобе", "Тараз",
        // "Павлодар", "Оскемен", "Семей", "Атырау", "Костанай",
        // "Кызылорда", "Орал", "Петропавловск", "Актау", "Темиртау",
        // "Туркестан", "Кокшетау", "Талдыкорган"
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
      },
      keyboardVisible: false,
      isSubmitting: false,
      originalHeight: window.innerHeight
    };
  },
  methods: {
    validateNumber(field) {
      if (field === 'building') {
        this[field] = this[field].replace(/[^0-9а-яА-Яa-zA-Z]/g, ''); 
        const match = this[field].match(/^(\d+)([а-яА-Яa-zA-Z]?)$/);
        this[field] = match ? match[0] : '';
      } else {
        this[field] = this[field].replace(/\D/g, '');
      }
    },
    formatPhone() {
      setTimeout(() => {
        let value = this.phone.replace(/\D/g, "").substring(0, 11);
        if (value.length === 0) {
          this.phone = "";
          return;
        }
        if (!value.startsWith("7")) value = "7" + value;
        let formatted = `+7 (${value.substring(1, 4)}`;
        if (value.length > 4) formatted += `) ${value.substring(4, 7)}`;
        if (value.length > 7) formatted += `-${value.substring(7, 9)}`;
        if (value.length > 9) formatted += `-${value.substring(9, 11)}`;
        this.phone = formatted;
      }, 10);
    },
    submitForm() {
      if (this.isSubmitting) return;
      this.isSubmitting = true;

      this.errorFields.selectedPrice = !this.selectedPrice;
      this.errorFields.selectedCity = !this.selectedCity;
      this.errorFields.phone = !this.phone || this.phone.length !== 18;
      this.errorFields.building = !this.building;
      this.errorFields.street = !this.street;

      if (Object.values(this.errorFields).some(error => error)) {
        this.isSubmitting = false;
        return;
      }

      const formData = {
        selected_dates: this.selectedDates,
        price_range: this.selectedPrice.replace(" ₸", "").replace(" ", ""),
        city: this.selectedCity,
        street: this.street,
        building: this.building,
        apartment: this.apartment,
        entrance: this.entrance,
        floor: this.floor,
        phone: this.phone.replace(/[^+0-9]/g, ""),
        comment: this.comment,
        user_id: 461357308,
      };

      localStorage.setItem("formData", JSON.stringify(formData));
      this.$emit("nextStep", formData);
      console.log(formData);
    },
    getUserId() {
      const urlParams = new URLSearchParams(window.location.search);
      this.userId = urlParams.get("user_id") || (window.Telegram?.WebApp?.initDataUnsafe?.user?.id) || null;
    }
  },
  mounted() {
    this.getUserId();
    window.visualViewport.addEventListener("resize", this.handleResize);
  },
  beforeUnmount() {
    window.visualViewport.removeEventListener("resize", this.handleResize);
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
  min-height: 100vh;
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
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  border-radius: 0;
  color: black;
}

select option {
  color: black;
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
  z-index: 1;
  position: relative;
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
  color: black;
  box-sizing: border-box;
}

@media (max-width: 600px) {
  .form-container {
    padding: 10px;
  }

  .input-group {
    margin-top: 25px;
  }

  .label {
    font-size: 12px;
    top: 5px;
  }

  .label.active {
    top: -10px;
    font-size: 10px;
  }

  input, select {
    padding: 8px 0;
    font-size: 12px;
  }

  button {
    margin-top: 30px;
    padding: 8px;
    font-size: 14px;
  }
}
</style>
    