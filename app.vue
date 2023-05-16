<template>
  <div class="score">{{ long }}</div>
  <div class="play" v-if="ready">
    <div
      class="block"
      v-for="i in 625"
      :class="{
        apple:
          apple.x == i - 1 - Math.floor((i - 1) / 25) * 25 &&
          apple.y == Math.floor((i - 1) / 25),
        active:
          (head.x == i - 1 - Math.floor((i - 1) / 25) * 25 &&
            head.y == Math.floor((i - 1) / 25)) ||
          bR[i - 1 - Math.floor((i - 1) / 25) * 25][Math.floor((i - 1) / 25)],
      }"
    ></div>
  </div>
</template>

<script lang="ts" setup>
const body = ref<{ x: number; y: number }[]>([]);
const bR = ref<boolean[][]>([]);
const head = ref<{ x: number; y: number }>({ x: 2, y: 2 });
const ready = ref(false);
const apple = ref({
  x: Math.round(Math.random() * 25),
  y: Math.round(Math.random() * 25),
});
const direct = ref(0);
const long = ref(0);
let moveInterval: any;

const setup = () => {
  ready.value = true;
  for (let i = 0; i < 25; i++) {
    bR.value[i] = [];
    for (let j = 0; j < 25; j++) {
      bR.value[i][j] = false;
    }
  }
};

const resetBody = () => {
  bR.value = [];
  for (let i = 0; i < 25; i++) {
    bR.value[i] = [];
    for (let j = 0; j < 25; j++) {
      bR.value[i][j] = false;
    }
  }
};

body.value.forEach(() => {});

const renderBody = () => {
  resetBody();
  for (let i = long.value; i >= 0; i--) {
    if (!body.value[i]) body.value[i] = { x: 0, y: 0 };
    if (i === 0) {
      body.value[i].x = head.value.x;
      body.value[i].y = head.value.y;
    } else if (body.value[i - 1]) {
      body.value[i].x = body.value[i - 1].x;
      body.value[i].y = body.value[i - 1].y;
    } else {
      body.value[i].x = 0;
      body.value[i].y = 0;
    }

    bR.value[body.value[i].x][body.value[i].y] = true;
  }
};

const genApple = () => {
  apple.value.x = Math.round(Math.random() * 25);
  apple.value.y = Math.round(Math.random() * 25);
};

const checkApple = () => {
  if (head.value.x === apple.value.x && head.value.y === apple.value.y) {
    genApple();
    long.value += 1;
  }
};

const checkColison = () => {
  body.value.forEach((item) => {
    if (item.x === head.value.x && item.y === head.value.y) {
      clearInterval(moveInterval);
      alert("Game Over");
    }
  });
};

onMounted(() => {
  setup();
  moveInterval = setInterval(() => {
    renderBody();
    if (direct.value === 0) {
      head.value.x += 1;
      if (head.value.x == 25) {
        head.value.x = 0;
      }
    } else if (direct.value === 1) {
      head.value.y += 1;
      if (head.value.y == 25) {
        head.value.y = 0;
      }
    } else if (direct.value === 2) {
      head.value.x -= 1;
      if (head.value.x == -1) {
        head.value.x = 24;
      }
    } else if (direct.value === 3) {
      head.value.y -= 1;
      if (head.value.y == -1) {
        head.value.y = 24;
      }
    }
    checkApple();

    checkColison();
  }, 140);

  document.addEventListener("keydown", (event) => {
    if (event.which === 39) {
      direct.value = 0;
    } else if (event.which === 40) {
      direct.value = 1;
    } else if (event.which === 37) {
      direct.value = 2;
    } else if (event.which === 38) {
      direct.value = 3;
    }
  });
});

onBeforeUnmount(() => {
  clearInterval(moveInterval);
});
</script>

<style lang="scss">
body {
  padding: 0;
  margin: 0;
}

.play {
  display: grid;
  gap: 2px;
  height: 100vh;
  aspect-ratio: 1;
  grid-template-columns: repeat(25, 1fr);
  grid-template-rows: repeat(25, 1fr);
  .block {
    height: 100%;
    width: 100%;
  }
  .active {
    background-color: green;
  }
}

.apple {
  border-radius: 50%;
  background-color: red !important;
}

.score {
  position: fixed;
  top: 0;
  background-color: white;
  color: black;
  font-family: Arial, Helvetica, sans-serif;
  height: 60px;
  width: 60px;
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px solid black;
}
</style>
