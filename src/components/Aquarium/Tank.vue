<script setup lang="ts">
import Form from "./Form.vue";
import Fish from "./Fish.vue";
import { ref, type Ref } from "vue";

const fishes = [
  { image: "golden-purple-fish.png", name: "Golden Purple Fish" },
  { image: "goldfish.png", name: "Golden Purple Fish" },
  { image: "guppie.png", name: "Guppie Fish" },
  { image: "tropical-fish.png", name: "Tropical Fish" },
  { image: "tuna.png", name: "Tuna Fish" },
];

type Fish = {
  fish: number;
  name: string;
  x?: number;
  y?: number;
};

const form: Ref<Fish> = ref({
  fish: 0,
  name: "",
});

const aquariumFishes = ref<Array<Fish>>([]);

const addFish = (fish: Fish) => {
  aquariumFishes.value.push(fish);
};

const removeFish = (id: number) => {
  aquariumFishes.value.splice(id, 1);
};
</script>
<template>
  <div
    class="aquarium-app w-full flex content-stretch max-w-full"
  >
    <div class="aquarium-controls bg-indigo-800 w-1/4 p-8">
      <Form :fishes="fishes" @add="addFish" />
    </div>
    <div class="aquarium-tank w-3/4 relative overflow-hidden" id="aquarium">
      <template
        v-for="({ fish, name }, index) in aquariumFishes"
        :key="`aquarium-fish-${index}`"
      >
        <Fish
          :id="index"
          :image="fishes[fish].image"
          :name="name"
          @remove="removeFish"
        />
      </template>
    </div>
  </div>
</template>
<style scoped>
.aquarium-tank {
  background-image: url("/images/aquarium/bg.jpg");
  background-size: cover;
  background-position: center;
}
</style>
