<template>


  <div>
    <table height="30" style="margin-top: 20px" class="vtl-table vtl-table-hover vtl-table-bordered vtl-table-responsive vtl-table-responsive-sm">
      <tr class="vtl-tbody-tr">
        <td class="vtl-tbody-td vtl-tbody-td0" ><input placeholder="ID" v-model="searchId"/></td>
        <td class="vtl-tbody-td vtl-tbody-td1" ><input placeholder="Название" v-model="searchTerm"/></td>
        <td class="vtl-tbody-td vtl-tbody-td2" ><input placeholder="Дата" v-model="searchDate"/></td>
        <td class="vtl-tbody-td vtl-tbody-td3" >
          <select  v-model="searchStatus">
            <option value="">All</option>
            <option value="CONFIRMED">CONFIRMED</option>
            <option value="REJECTED">REJECTED</option>
            <option value="AUTHORIZED">AUTHORIZED</option>
          </select>
        </td>
      </tr>
    </table>
    <table-lite
        :is-loading="table.isLoading"
        :columns="table.columns"
        :rows="table.rows"
        :total="table.totalRecordCount"
        :sortable="table.sortable"
        :messages="table.messages"
        @do-search="doSearch"
        @is-finished="table.isLoading = false"
    ></table-lite>
  </div>
</template>

<script>
import {defineComponent, reactive, ref, watch} from "vue";
import TableLite from "vue3-table-lite";
import axios from "axios";

axios.defaults.headers.common = {'Authorization': `Bearer d6a8c8dc229946c2596590a0f5aa7eca`}
// import VueTableLite from 'vue3-table-lite'

export default defineComponent({
  name: "App",
  components: {TableLite},
  setup() {
    const searchTerm = ref(""); // Search text
    const searchId = ref(""); // Search text
    const searchDate = ref(""); // Search text
    const searchStatus = ref(""); // Search text

    let filters = {};
    let limit_total = 10;

    // (номер заказа, название продукта, дата начала, статус).
    const table = reactive({
      isLoading: false,
      columns: [
        {
          label: "#",
          field: "id",
          width: "3%",
          sortable: true,
          isKey: true,
        },
        {
          label: "Название",
          field: "subscription_id",
          width: "10%",
          sortable: true,
        },
        {
          label: "Дата начала",
          field: "date_add",
          width: "15%",
          sortable: true,
        },
        {
          label: "Статус",
          field: "status",
          width: "15%",
          sortable: true,
        },
      ],
      rows: [],
      totalRecordCount: 0,
      sortable: {
        order: "id",
        sort: "asc",
      },
    });

    /**
     * Search Event
     */
    const doSearch = (offset, limit, order, sort) => {
      table.isLoading = true;

      limit_total = limit;
      // Start use axios to get data from Server
      let sort_order = sort === "desc" ? "-" : "";
      let per_page = offset / limit;
      let url = 'https://dev.whatcrm.net/lk/tinkoffs?page=' + per_page + '&limit=' + limit + '&sort=' + sort_order + order;
      if (filters) {
        console.log(filters);
        const params = new URLSearchParams(filters);
        url += "&";
        url += params.toString();
      }
      axios.get(url)
          .then((response, headers) => {
            console.log(response);

            // refresh table rows
            table.rows = response.data;
            table.totalRecordCount = response.headers['x-pagination-total-count'] * 1;
            table.sortable.order = order;
            table.sortable.sort = sort;
          });
    };

    /**
     * Use vue.js watch to watch your state's change
     */
    watch(
        () => searchId.value,
        (val) => {
          filters['filter[id]'] = val;
          if (!val) delete filters['filter[id]'];
          doSearch(0, limit_total, table.sortable.order, table.sortable.sort);
        }
    );
    watch(
        () => searchTerm.value,
        (val) => {
          filters['filter[subscription_id]'] = val;
          console.log("VALUE", val);
          if (!val) delete filters['filter[subscription_id]'];
          doSearch(0, limit_total, table.sortable.order, table.sortable.sort);
        }
    );
    watch(
        () => searchStatus.value,
        (val) => {
          filters['filter[status]'] = val;
          if (!val) delete filters['filter[status]'];
          doSearch(0, limit_total, table.sortable.order, table.sortable.sort);
        }
    );
    watch(
        () => searchDate.value,
        (val) => {
          filters['filter[date_add]'] = val;
          if (!val) delete filters['filter[date_add]'];
          doSearch(0, limit_total, table.sortable.order, table.sortable.sort);
        }
    );
    // First get data
    doSearch(0, limit_total, "id", "desc");

    return {
      searchId,
      searchDate,
      searchTerm,
      searchStatus,
      table,
      doSearch,
    };
  },
});
</script>