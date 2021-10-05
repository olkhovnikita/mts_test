<template>
  <div class="q-pa-md">
    <div class="q-pa-md">
      <div class="q-gutter-md" style="max-width: 300px"> </div>
    </div>
    <q-table
      title="Курс Валют"
      :rows="rows"
      :columns="columns"
      row-key="name"
      :filter="filter"
      :binary-state-sort="true"
      :loading="loading"
      no-data-label="Нет данных"
      loading-label="Загрузка"
      no-results-label="Нет подходящих данных"
    >
      <template v-slot:top-right>
        <q-input
          borderless
          dense
          debounce="300"
          v-model="filter"
          placeholder="Искать по наименованию"
        >
          <template v-slot:append>
            <q-icon name="search" />
          </template>
        </q-input>
      </template>
    </q-table>
  </div>
</template>

<script>
const axios = require("axios").default;
const parser = require("xml2json-light");
import { onBeforeMount, onMounted, ref, watch } from "vue";

export default {
  name: "Table",
  props: ["date"],
  setup(props) {
    const url = "http://www.cbr.ru/scripts/XML_daily.asp?date_req=";
    const columns = [
      {
        name: "CharCode",
        required: true,
        label: "Код",
        align: "left",
        field: (row) => row.CharCode,
        format: (val) => `${val}`,
        sortable: true,
      },
      {
        name: "CharName",
        required: true,
        label: "Наименование",
        align: "left",
        field: (row) => row.Name,
        format: (val) => `${val}`,
        sortable: true,
      },
      {
        name: "Nominal",
        label: "Номинал",
        field: "Nominal",
        sortable: true,
        sort: (a, b) => parseInt(a, 10) - parseInt(b, 10),
      },
      {
        name: "Value",
        label: "Значение",
        field: "Value",
        sortable: true,
        sort: (a, b) => parseInt(a, 10) - parseInt(b, 10),
      },
    ];

    const rows = ref([]);
    const filter = ref("");

    let errored = false;
    const loading = ref(false);

    function fetchData(d) {
      axios
        .get(url + d)
        .then((response) => {
          let parsed = response.data;
          const data = parser.xml2json(parsed);
          rows.value = data.ValCurs.Valute;
        })
        .catch((error) => {
          console.log(error);
          errored = true;
        })
        .finally(() => (loading.value = false));
    }

    onBeforeMount(() => {
      const userTable = localStorage.getItem("userTable");
      if (userTable) {
        columns.sort = JSON.parse(userTable);
      }
    });

    watch(
      () => props.date,
      () => {
        loading.value = true;
        setTimeout(() => {
          loading.value = false;
          fetchData(props.date);
        }, 1000);
      }
    );

    watch(
      () => filter.value,
      () => {
        setTimeout(() => {
          rows.value.filter((item) =>
            item.Name.toLowerCase().includes(filter.value.toLowerCase())
          );
        }, 1000);
      }
    );

    onMounted(() => {
      fetchData(props.date);
      console.log(errored);
    });

    return {
      columns,
      loading,
      filter,
      rows,
    };
  },
};
</script>
<style scoped></style>
