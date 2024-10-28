<script setup>
import SortBar from './components/SortBar.vue'
import LessonsPage from './components/LessonsPage.vue'
import lessons from './assets/lessons.json'
import CartPage from './components/CartPage.vue'
import CheckoutModal from './components/CheckoutModal.vue'
import {ref, watch} from 'vue'
const lessonsList = ref(lessons)
const cartsList = ref([])
const showCart = ref(false)
const sortOrder = ref('asc')
const sortField = ref('none')
const modalMessage = ref('')  // To store modal message
const modalTitle = ref('')    // To store modal title
const showCheckoutModal = ref(false)

const checkout = (customerName, customerPhone) => {
  cartsList.value = []
  toggleShowCart()

  // Show modal after successful checkout
  modalTitle.value = 'Checkout Successful'
  modalMessage.value = `Thank you, ${customerName}. Your order is confirmed! with following number: ${customerPhone}`
  toggleShowCheckoutModal()
}
const toggleShowCheckoutModal = () => {
  showCheckoutModal.value = !showCheckoutModal.value
}
const toggleShowCart = () => {
  showCart.value = !showCart.value
}
const updateSort = ({field, order}) => {
  sortField.value = field
  sortOrder.value = order
}
const addToCart = (lessonId) => {
  const index = lessonsList.value.findIndex(lesson => lesson.id === lessonId)
  if(index === -1)
    return

  const updatedLessons = [...lessonsList.value]
  if (updatedLessons[index].Spaces > 0) {
    updatedLessons[index].Spaces -= 1

    
    const cartIndex = cartsList.value.findIndex(item => item.id === updatedLessons[index].id)

    if (cartIndex !== -1) {
      
      cartsList.value[cartIndex].Spaces += 1
    } else {
      let temp = { ...updatedLessons[index] };
    temp.Spaces = 1;
    cartsList.value.push(temp);

    }
  }
  lessonsList.value = updatedLessons

}

const removeFromCart = (index) => {
  const cartItem = cartsList.value[index]
  cartsList.value.splice(index, 1)
  const lessonIndex = lessonsList.value.findIndex(item => item.id === cartItem.id)

  if (lessonIndex !== -1) {
    lessonsList.value[lessonIndex].Spaces += cartItem.Spaces 
  }

  if(cartsList.value.length === 0){
    toggleShowCart()
  }
}

watch([sortOrder, sortField, lessonsList], () => {
  
  
  if(sortField.value == 'none')
    return

  lessonsList.value.sort((a, b) => {
    let compareA = a[sortField.value]
    let compareB = b[sortField.value]

    // Handle numeric sorting for fields like Price and Spaces
    if (sortField.value === 'Price') {
      compareA = parseInt(compareA.replace('£', '')) || compareA
      compareB = parseInt(compareB.replace('£', '')) || compareB
    }
    else if(sortField.value === 'Spaces'){
      compareA = parseInt(compareA) || compareA
      compareB = parseInt(compareB) || compareB
    }

    if (sortOrder.value === 'asc') {
      return compareA > compareB ? 1 : -1
    } else {
      return compareA < compareB ? 1 : -1
    }
  })})

</script>

<template>

  <main>
    <SortBar @updateSort="updateSort"/>
    <LessonsPage :lessons="lessonsList" @addToCart="addToCart" />
    <button @click="toggleShowCart" :disabled="cartsList.length === 0" class="cart-button">
        🛒 Cart ({{ cartsList.length }})
    </button>
    <CartPage v-if="showCart" :cart="cartsList" @removeFromCart="removeFromCart" @checkout="checkout" />
    <CheckoutModal v-if="showCheckoutModal" :title="modalTitle" :message="modalMessage" :onClose="toggleShowCheckoutModal"/>
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

.cart-button {
    position: fixed;
    bottom: 20px;
    right: 20px;
    background-color: #007bff;
    color: #fff;
    border: none;
    padding: 15px 25px;
    font-size: 1rem;
    font-weight: bold;
    border-radius: 50px;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    transition: background-color 0.3s ease, transform 0.2s ease;
}

.cart-button:hover {
    background-color: #0056b3;
    transform: scale(1.05);
}

.cart-button:active {
    background-color: #003d82;
    transform: scale(0.95);
}

.cart-button:disabled {
    background-color: #888; /* Change to a distinct grey */
    color: #ccc; /* Lighten text color */
    opacity: 0.6; /* Add slight transparency */
    cursor: not-allowed;
    text-decoration: line-through; /* Optional: strikethrough for added clarity */
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}


</style>
