<script>
import axios from "axios";
import {useRoute} from 'vue-router';
import Order from "@/components/Order.vue";
import IconLoading from "@/components/icons/IconLoading.vue";

axios.defaults.headers.common = {'Authorization': `Bearer d6a8c8dc229946c2596590a0f5aa7eca`}
// import VueTableLite from 'vue3-table-lite'

export default {
  components: {IconLoading, Order},
  data() {
    return {
      orderData: []
    }
  },
  mounted() {
    const route = useRoute()
    let url = `https://dev.whatcrm.net/lk/tinkoffs/` + route.params.id;
    axios.get(url)
        .then((response, headers) => {
          console.log(response);
          this.orderData = response.data;
        });
  }
}
</script>

<template>
  <div style="margin-top: 20px;">
    <div v-if="!orderData">
      <IconLoading/>
    </div>

    <Order v-bind:orderData="orderData" v-if="orderData" />

  </div>
</template>