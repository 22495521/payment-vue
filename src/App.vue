<script setup>
import axios from 'axios'
import { ref } from 'vue'

const order = ref({
  Email: 'd22495521@gmail.com',
  Amt: 1000,
  ItemDesc: '測試商品'
})

const formData = ref(null)

const { VITE_PAY_HOST } = import.meta.env

const submit = async () => {
  try {
    const payload = { ...order.value }
    // 向后端发送数据进行加密
    const BackResult = await axios.post(`${VITE_PAY_HOST}/createOrder`, payload)
    // 将加密后的数据存储在 formData 中
    formData.value = BackResult.data.data
    console.log(formData.value)
  } catch (error) {
    console.log(error)
  }
}
</script>

<template>
  <div>
    <div>
      email
      <input type="email" v-model="order.email" />
    </div>
    <div>
      Amt
      <input type="number" v-model="order.Amt" />
    </div>
    <div>
      ItemDesc
      <input type="text" v-model="order.ItemDesc" />
    </div>
    <button type="button" @click="submit">送出</button>
  </div>

  <div v-if="formData">
    <form action="https://ccore.newebpay.com/MPG/mpg_gateway" method="post">
      <input type="text" name="MerchantID" :value="formData.MerchantID" />
      <input type="text" name="TradeSha" :value="formData.order.shaEncrypt" />
      <input type="text" name="TradeInfo" :value="formData.order.aesEncrypt" />
      <input type="text" name="TimeStamp" :value="formData.order.TimeStamp" />
      <input type="text" name="Version" :value="formData.Version" />
      <input type="text" name="NotifyUrl" :value="formData.order.NotifyUrl" />
      <input type="text" name="ReturnUrl" :value="formData.order.ReturnUrl" />
      <input type="text" name="MerchantOrderNo" :value="formData.order.MerchantOrderNo" />
      <input type="text" name="Amt" :value="formData.order.Amt" />
      <input type="text" name="ItemDesc" :value="formData.order.ItemDesc" />
      <input type="email" name="Email" :value="formData.order.email" />
      <button type="submit">送出</button>
    </form>
  </div>
</template>

<style scoped></style>
