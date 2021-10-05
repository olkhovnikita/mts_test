<template>
  <Calendar @pickNewDate="pickNewDate" :date="date" />
  <Table :date="date" />
</template>

<script>
import Calendar from "./components/Calendar.vue";
import Table from "./components/Table.vue";
import { onBeforeMount, ref } from "vue";

export default {
  name: "App",

  components: {
    Calendar,
    Table,
  },

  setup() {
    const date = ref("");

    const gotDate = ref(false);

    onBeforeMount(() => {
      const userDate = localStorage.getItem("userDate");
      if (userDate) {
        gotDate.value = true;
        date.value = JSON.parse(userDate);
      }
    });

    const pickNewDate = (v) => {
      date.value = v.value;
      localStorage.setItem("userDate", JSON.stringify(date.value));
      console.log(date.value);
    };

    return {
      pickNewDate,
      date,
    };
  },
};
</script>
