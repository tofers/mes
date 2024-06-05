<template>

  просмотр заявки {{ $route.params.id }}
  <a href="/orders">вернуться назад</a>
  <pre>
    {{ orderData }}
  </pre>
</template>


<script>
import axios from "axios";
import {useRoute} from 'vue-router'

axios.defaults.headers.common = {'Authorization': `Bearer d6a8c8dc229946c2596590a0f5aa7eca`}
// import VueTableLite from 'vue3-table-lite'

export default {
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