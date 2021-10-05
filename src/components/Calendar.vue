<template>
  <div class="q-pa-md">
    <div class="q-gutter-md row items-start">
      <q-date
        mask="DD/MM/YYYY"
        v-model="pickedDate"
        @click="pickNewDate"
        minimal
      />
    </div>
  </div>
</template>

<script>
import { onBeforeMount, ref } from "vue";
export default {
  name: "Calendar",

  props: ["date"],

  emits: ["pickNewDate"],

  setup(props, { emit }) {
    const pickedDate = ref(new Date());

    onBeforeMount(() => {
      pickedDate.value = props.date;
    });

    const pickNewDate = () => {
      emit("pickNewDate", pickedDate);
    };

    return {
      pickedDate,
      pickNewDate,
    };
  },
};
</script>

<style scoped></style>
