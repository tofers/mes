<script setup>
import {ref} from 'vue'
import axios from "axios";
import {useRoute} from "vue-router";

const submitted = ref(false)
const orderId = ref(false)
axios.defaults.headers.common = {'Authorization': `Bearer d6a8c8dc229946c2596590a0f5aa7eca`}
const submitHandler = async (credentials) => {

  credentials['date_add'] = credentials['date'] + " " + credentials['time'];

  let orderId;
  let url = `https://dev.whatcrm.net/lk/tinkoffs/` + credentials['id'];
  const res = await axios.put(url, credentials)
  // do some login things now
  console.log(res)
  submitted.value = true
  alert("Данные успешно сохранены")
}

</script>
<script>
import {useRoute} from "vue-router";
import axios from "axios";

export default {
  data() {
    return {
      renderComponent: false,
      orderData: {
        date: String,
        time: String,
        id: String,
        status: String,
        subscription_id: String,
      },
      orderId: Number
    }
  },
  mounted() {
    const route = useRoute();
    let url = `https://dev.whatcrm.net/lk/tinkoffs/` + route.params.id;
    axios.get(url)
        .then((response, headers) => {
          let orderData = response.data;

          orderData.date = response.data['date_add'].split(" ")[0];


          let time = response.data['date_add'].split(" ")[1];
          orderData.time = time.split(':')[0] + ":" + time.split(':')[1];
          this.orderData = orderData;

          console.log(this.orderData);
          this.renderComponent = true;


        });
  }
}
</script>
<template>
  <FormKit
      type="form"
      v-if="renderComponent"
      id="registration-example"
      :form-class="submitted ? 'hide' : 'show'"
      submit-label="Register"
      @submit="submitHandler"
      :actions="false"
      :value="orderData"
  >
    <h1 class="text-2xl font-bold mb-2">Просмотр заявки #{{ $route.params.id }}</h1>
    <FormKit
        type="hidden"
        name="id"
    />
    <FormKit
        type="text"
        name="subscription_id"
        label="Название"
        validation="required"
    />
    <FormKit
        type="date"
        name="date"
        label="Дата начала"
        validation="required"
    />

    <FormKit
        type="time"
        name="time"
        label="Время начала"
        validation="required"
    />
    <FormKit
        type="select"
        name="status"
        label="Статус"
        validation="required"
        :options="[
          'CONFIRMED',
          'REJECTED',
          'CONFIRMED',
          'AUTHORIZED',
        ]"
    />


    <FormKit type="submit" label="Cохранить"/>
  </FormKit>
  <pre>
    {{ orderData }}
  </pre>
  <div v-if="submitted">
    <h2 class="text-xl text-green-500">Submission successful!</h2>
  </div>
</template>

<style>
@media (min-width: 400px) {
  .double {
    display: flex;
    justify-content: space-between;
  }
}

form {
  width: 420px;
  padding: 1.5em 1.5em 0 1.5em;
  border: 1px solid #e4e4e4;
  border-radius: 1em;
  margin: 0 auto 1em auto;
  background-color: #e4e4e4;
  color: #181818;
}
</style>