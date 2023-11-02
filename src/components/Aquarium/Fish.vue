<script setup lang="ts">
import { ref, onMounted, computed, watch, onBeforeUnmount, type Ref } from 'vue';

const timeout: Ref<number> = ref(0);

const transition = computed(() => `all ${timeout.value / 1000}s linear`);

const AquariumEL = ref();
const fishDOMEl = ref();

const minMilliseconds: number = 4000;
const maxMilliseconds: number = 10000;

const isDied: Ref<boolean> = ref(false);

const directionLeft = ref(false);

const hungryTimeout = ref(0);
const isHungry = ref(false);

const deathTO: Ref<any> = ref(0);
const hungryTO: Ref<any> = ref(0);
const runningTO: Ref<any> = ref(0);

const position: Ref<{x: number, y:number}> = ref({
    x: 0,
    y: 0
});

const props = defineProps({
    id: {
        type: Number,
        required: true
    },
    image: {
        type: String,
        required: true
    },
    name: {
        type: String,
        required: true,
    }
});

const emits = defineEmits(['remove']);

function runTimeout() {
    timeout.value = generateTimeout();
    runningTO.value = setTimeout(() => {
        runTimeout();
        moveFish();
    }, timeout.value);
}

function generateTimeout()
{
    return Math.floor(Math.random() * minMilliseconds) + maxMilliseconds;
}

function moveFish() {
    position.value = {
        x: generateXPosition(),
        y: generateYPosition()
    };
}

function generateXPosition() {
    let x = Math.random() * AquariumEL.value.offsetWidth - fishDOMEl.value.clientWidth;

    let xPadded = x < 0 ? 0 : x;
    
    if(position.value.x === 0) return xPadded;

    if(xPadded > AquariumEL.value.offsetWidth - fishDOMEl.value.clientWidth) {
        return AquariumEL.value.offsetWidth - fishDOMEl.value.clientWidth;
    }

    return xPadded;
}

function generateYPosition() {
    let y = Math.random() * AquariumEL.value.offsetHeight - fishDOMEl.value.clientHeight;

    let yPadded = y < 0 ? 0 : y;
    
    if(position.value.y === 0) return yPadded;

    if(yPadded > AquariumEL.value.offsetHeight - fishDOMEl.value.clientHeight) {
        return AquariumEL.value.offsetHeight - fishDOMEl.value.clientHeight;
    }

    return yPadded;
}

onMounted(() => {
    AquariumEL.value = document.querySelector('#aquarium');
    runTimeout();
    moveFish();
    hungryTO.value = runFeedMeTimeout();
});

onBeforeUnmount(() => {
    clearTimeout(runningTO.value);
    clearTO();
})

watch(() => position.value.x, (newX, oldX) => {
    if(newX < oldX) {
        directionLeft.value = true;
    }
    else {
        directionLeft.value = false;
    }
});

function runFeedMeTimeout() {
    hungryTimeout.value = generateTimeout();
    return setTimeout(() => {
        isHungry.value = true;
        deathTO.value = deathTimeout();
    }, hungryTimeout.value);
}

function deathTimeout() {
    return setTimeout(() => {
        isDied.value = true;
        isHungry.value = false;
        position.value.y = (AquariumEL.value.offsetHeight - 150);
        clearTimeout(deathTO.value);
        clearTimeout(hungryTO.value);
        clearTimeout(runningTO.value);
    }, 20000)
}

function clearTO() {
    isHungry.value = false;
    clearTimeout(deathTO.value);
    clearTimeout(hungryTO.value);
    runFeedMeTimeout();
}

const size = computed(() => {
  const random = Math.random();
  if (random <= 0.2) {
    return "w-16";
  } else if (random <= 0.5) {
    return "w-24";
  } else if (random >= 0.9) {
    return "w-48";
  }
  return "w-32";
});

</script>
<template>
    <span class="w-auto inline absolute text-center fish"
    :style="{
        left: `${position.x}px`,
        top: `${position.y}px`,
        transition: transition
    }"
    ref="fishDOMEl" @click.stop="clearTO">
        <span v-show="isHungry" class="bg-gray-300 text-black p-2 rounded">Feed me...</span>
        <img :src="`/images/aquarium/${isDied ? 'dead.png' : image}`" alt="" :class="size" :style="{
            transform: `scaleX(${(directionLeft ? -1 : 1)})`
        }"/>
        <span class="bg-black/90 text-xs p-1 text-white">{{ name }}</span>
    </span>
</template>