<template>
  <div
    ref="carRef"
    :class="[
      `${
        isVertical
          ? 'absolute top-0 right-8 w-10 h-16 -mt-16'
          : 'absolute left-0 top-5 w-16 h-10 -ml-16'
      }`,
      'test-text opacity-90 rounded  border border-yellow-400 bg-yellow-500 grid place-items-center',
    ]"
  >
    <span
      class="text-xs"
      :style="`${
        isVertical
          ? 'word-wrap: break-word; width: 0.6rem; line-height: 0.65rem;'
          : ''
      }`"
    >
      {{ carName ? carName : loading ? "※※※※※" : props.carInfo.carCounter }}
    </span>
  </div>
</template>

<script setup>
import { onMounted, watch, ref } from "vue";
import { v4 as uuid } from "uuid";
const props = defineProps([
  "stopPosition",
  "roadInfo",
  "carInfo",
  "isVertical",
]);
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
  // const [moveY] = props.stopPosition.moveY.split("px");
  const [moveDistance] = props.isVertical
    ? props.stopPosition.moveY.split("px")
    : props.stopPosition.moveX.split("px");
  let moveTo = 0;
  const move = () => {
    // TODO: 计算 , 根据时间和距离计算每一步运行多少 px
    moveTo += 0.2;
    if (moveTo > moveDistance) {
      // 到达 stop 位置, 运行 stop 的 callback
      console.log(" 运行到 stop station , 执行回调 ");
      getCarName().then((name) => {
        carName.value = name;
        props.isVertical
          ? carDOM.classList.add("car-move-y-end")
          : carDOM.classList.add("car-move-x-end");
      });

      return;
    } else {
      carDOM.style.transform = props.isVertical
        ? `translateY(${moveTo}px)`
        : `translateX(${moveTo}px)`;
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
  if (!props.stopPosition || Object.keys(props.stopPosition).length === 0)
    return;
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
