<script setup lang="ts">
import { ref, type Ref } from "vue";

const emits = defineEmits(["add"]);

type Fish = {
  image: string;
  name: string;
};

const props = defineProps({
  fishes: {
    type: Array<Fish>,
    required: true,
  },
});

const form: Ref<{
  fish: number;
  name: string;
}> = ref({
  fish: 0,
  name: props.fishes[0].name,
});

const setFishName = () => {
  form.value.name =
    props.fishes.find((fish, index) => index === form.value.fish)?.name || "";
};

const onSubmit = () => {
  emits("add", { ...form.value });
};
</script>
<template>
  <form @submit.prevent="onSubmit">
    <div class="mb-12">
      <label class="text-white">Fish type:</label>
      <ul
        class="mt-8 grid grid-flow-row grid-cols-2 text-center items-center justify-items-center gap-8"
      >
        <li v-for="({ image }, index) in fishes" :key="`fish-type-${index}`" :class="{
            'fish-selected': index === form.fish
        }">
          <input
            :id="`fish-type-${index}`"
            type="radio"
            name="fish-type"
            :value="index"
            v-model="form.fish"
            @change="setFishName"
            class="invisible"
          />
          <label :for="`fish-type-${index}`">
            <img
              :src="`/images/aquarium/${image}`"
              alt=""
              class="w-32"
            />
          </label>
        </li>
      </ul>

    </div>
    <div class="mb-12">
      <label class="block mb-4 text-white">Name:</label>
      <input
        type="text"
        class="w-96 max-w-full clear-both p-2 text-black"
        v-model="form.name"
      />
    </div>
    <button type="submit" class="bg-red-500 text-white w-96 max-w-full p-2 text-xl font-bold">
      Add fish
    </button>
  </form>
</template>
<style scoped>
.fish-selected {
  filter: drop-shadow(2px 2px 0 #00cfff) drop-shadow(-2px -2px 0 #00cfff)
    drop-shadow(2px -2px 0 #00cfff) drop-shadow(-2px 2px 0 #00cfff);
}
</style>