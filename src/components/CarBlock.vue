<template>
  <div
    ref="carRef"
    class="test-text absolute top-0 right-8 opacity-90 rounded w-10 h-16 border border-yellow-400 bg-yellow-500 grid place-items-center"
  >
    <span
      class="text-sm"
      style="word-wrap: break-word; width: 0.875rem; line-height: 0.875rem"
    >
      {{ carName ? carName : loading ? "loading" : props.carInfo.carCounter }}
    </span>
  </div>
</template>

<script setup>
import { onMounted, watch, ref } from "vue";
import { v4 as uuid } from "uuid";
const props = defineProps(["stopPosition", "roadInfo", "carInfo"]);
const carRef = ref(null);
const carName = ref("");

const loading = ref(false);
const getCarName = () => {
  return new Promise((resolve) => {
    loading.value = true;
    setTimeout(() => {
      resolve(uuid().slice(0, 5));
      loading.value = false;
    }, 2000);
  });
};

// 动画运行到 stop station
const runToStopStation = () => {
  const carDOM = carRef.value;
  const [moveY] = props.stopPosition.moveY.split("px");
  let moveTo = 0;
  const move = () => {
    // 计算 , 根据时间和距离计算每一步运行多少 px
    moveTo += 0.2;
    if (moveTo > moveY) {
      // 到达 stop 位置, 运行 stop 的 callback
      console.log(" 运行到 stop station , 执行回调 ");
      getCarName().then((name) => {
        carName.value = name;
        carDOM.classList.add("car-move-end");
      });

      return;
    } else {
      carDOM.style.transform = `translateY(${moveTo}px)`;
      requestAnimationFrame(move);
    }
  };
  requestAnimationFrame(move);
};

onMounted(() => {
  // const carDOM = carRef.value;
  // console.log(
  //   "carDOM :>> ",
  //   carDOM,
  //   "roadInfo:",
  //   props.roadInfo,
  //   "stopPosition:",
  //   props.stopPosition
  // );
  runToStopStation();
});

watch(
  () => props.roadInfo,
  (_roadInfo, prev) => {
    if (!prev && _roadInfo) {
      console.log("_roadInfo :>> ", _roadInfo, "prev:", prev);
    }
  }
);

watch(
  () => props.stopPosition,
  (_stopPosition, prev) => {
    console.log("_stopPosition :>> ", _stopPosition, prev);
    if (!_stopPosition) return;
    // runToStopStation();
  }
);
</script>

<style lang="scss" scoped></style>
