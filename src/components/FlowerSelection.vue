<template>
  <div class="container">
    <h2>ВЫБЕРИТЕ ПРЕДПОЧИТАЕМЫЕ ЦВЕТЫ</h2>

    <div v-if="!orderSent" class="flower-grid">
      <div
        v-for="flower in flowers"
        :key="flower.id"
        class="flower-card"
        :class="{ selected: selectedFlowers.includes(flower.id), disabled: isDisabled(flower.id) }"
        @click="toggleSelection(flower.id)"
      >
        <div class="image-container">
          <img :src="flower.image" alt="Цветок" class="flower-image" />
          <div v-if="!selectedFlowers.includes(flower.id)" class="plus-circle">
            <span class="plus-icon">+</span>
          </div>
        </div>
        <div class="flower-info">
          <span class="flower-name">{{ flower.name }}</span>
          <span class="flower-price">{{ flower.price }} тг</span>
        </div>
      </div>
    </div>

    <div class="button-container" v-if="!orderSent">
      <button
        class="continue-btn"
        :class="{ 'selected-btn': selectedFlowers.length > 0 }"
        :disabled="selectedFlowers.length === 0 || isSubmitting"
        @click="submitSelection"
      >
        {{ isSubmitting ? "ОТПРАВКА..." : "ПРОДОЛЖИТЬ" }}
      </button>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      flowers: [],
      selectedFlowers: [],
      maxSelectable: JSON.parse(localStorage.getItem("selectedDates"))?.length || 0,
      isSubmitting: false,
      orderSent: false,
    };
  },
  methods: {
    toggleSelection(flowerId) {
      if (this.selectedFlowers.includes(flowerId)) {
        this.selectedFlowers = this.selectedFlowers.filter((id) => id !== flowerId);
      } else if (this.selectedFlowers.length < this.maxSelectable) {
        this.selectedFlowers.push(flowerId);
      }
    },

    async submitSelection() {
      if (this.isSubmitting) return;
      this.isSubmitting = true;

      const formData = JSON.parse(localStorage.getItem("formData")) || {};
      formData.selected_flowers = this.selectedFlowers;

      try {
        const response = await axios.post("https://bloom-backend-production.up.railway.app/orders", formData);
        console.log("Заказ успешно отправлен:", response.data);

        this.orderSent = true;
        localStorage.removeItem("formData");
        this.$emit("selectionConfirmed");
      } catch (error) {
        console.error("Ошибка при отправке заказа:", error);
      } finally {
        this.isSubmitting = false;
      }
    },

    isDisabled(flowerId) {
      return this.selectedFlowers.length >= this.maxSelectable && !this.selectedFlowers.includes(flowerId);
    },

    async fetchFlowers() {
      try {
        const response = await axios.get("https://bloom-backend-production.up.railway.app/flowers");
        this.flowers = response.data;
      } catch (error) {
        console.error("Ошибка при загрузке данных о цветах:", error);
      }
    },
  },
  mounted() {
    localStorage.removeItem("selectedFlowers");
    this.fetchFlowers();
  },
};
</script>

  
  <style scoped>
  .container {
    text-align: center;
    padding: 20px;
    font-family: "SF Pro", sans-serif;
    position: relative;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
  }
  
  h2 {
    font-size: 16px;
    text-transform: uppercase;
    margin-bottom: 15px;
  }
  
  .flower-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
    justify-content: center;
    flex-grow: 1;
    overflow-y: auto;
  }
  
  .flower-card {
    width: 170px;
    height: 305px;
    border: 1px solid #000;
    cursor: pointer;
    transition: 0.3s;
    box-sizing: border-box;
  }
  
  .flower-card.disabled {
    opacity: 0.5;
    pointer-events: none;
  }
  
  .flower-card.selected {
    border: 1px solid #ff4081;
    filter: brightness(0.8);
  }
  
  .image-container {
    position: relative;
    width: 100%;
    height: 254px;
  }
  
  .flower-image {
    width: 100%;
    height: 254px;
    object-fit: cover;
    display: block;
  }
  
  .plus-circle {
    position: absolute;
    bottom: 10px;
    left: 50%;
    transform: translateX(-50%);
    width: 24px;
    height: 24px;
    background: #fff;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  .plus-icon {
    font-size: 20px;
    font-weight: bold;
    color: #000;
  }
  
  .flower-info {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    padding: 10px;
    text-align: left;
    text-transform: uppercase;
    font-size: 10px;
  }
  
  .flower-name {
    display: block;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  
  .flower-price {
    display: block;
    margin-top: 4px;
    font-weight: bold;
  }
  
  .button-container {
    position: fixed;
    bottom: 20px;
    left: 50%;
    transform: translateX(-50%);
    width: 100%;
    max-width: 350px;
  }
  
  .continue-btn {
    width: 100%;
    padding: 10px;
    border: 1px solid black;
    background: transparent;
    font-weight: 500;
    text-transform: uppercase;
    cursor: pointer;
    color: black;
    box-sizing: border-box;
  }
  
  .selected-btn {
    background-color: white;
    color: black;
    border: 1px solid black;
  }
  
  .continue-btn:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
  </style>
