<template>
  <div
    id="road"
    ref="roadRef"
    :class="[
      `${isVertical ? 'w-40 h-auto' : 'flex flex-row h-40 w-auto'}`,
      ' bg-sky-800 mt-10 relative overflow-hidden',
    ]"
  >
    <!-- the start block -->
    <div
      :class="[
        `${
          isVertical
            ? 'w-full h-20 flex flex-col justify-center items-start pl-5'
            : 'w-20 h-full flex flex-col justify-end items-center pb-5'
        }`,
        'border-red-500',
      ]"
    >
      <div>start</div>
    </div>

    <CarBlock
      v-for="car in cars"
      :key="car.id"
      :carInfo="car"
      :roadInfo="roadInfo"
      :stopPosition="stopPosition"
      :isVertical="isVertical"
    />
    <!-- stations  -->
    <div
      v-for="station in stations"
      :key="station.stationIndex"
      :class="[
        `${station.stop ? 'bg-orange-600' : ' '}`,
        `${
          isVertical
            ? 'flex justify-start items-center w-full h-14 pl-5'
            : 'w-14 h-full flex flex-col justify-end items-center pb-5'
        }`,
        'border-yellow-300 ',
      ]"
    >
      <div
        v-if="station.stop"
        ref="stopStationRef"
        :class="[`${isVertical ? '-mt-14' : '-ml-14'}`, `w-0 h-0 bg-green-500`]"
      ></div>
      <div>
        {{ station.stationName }}
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, getCurrentInstance, computed } from "vue";
import CarBlock from "./CarBlock.vue";
export default {
  props: {
    cars: Object,
    direction: String,
    stopStationIndex: Number,
  },
  components: {
    CarBlock,
  },

  setup(props) {
    // const { cars } = toRefs(props);
    const roadRef = ref(null);
    const stopStationRef = ref(null);
    const roadInfo = ref({});
    const stopPosition = ref({});
    const padString = (str, len = 3) => {
      if (str.toString().length >= len) return str.toString();
      const _length = len - str.toString().length;
      return Array(_length).fill(0).join("") + str.toString();
    };

    // 创建 stations
    const stations = new Array(11).fill(0).map((_, index) => {
      return {
        stationIndex: index,
        stationName: padString(index),
        stop: index === props.stopStationIndex,
      };
    });

    onMounted(() => {
      console.log("props.cars :>> ", props.cars);
      const roadDOM = roadRef.value;
      // TODO: 因为把车子用负的 margin移动到了 road 外面, 所以, 还需要加上车子的长度, 这里先用固定值 h-16 == 64
      const carLength = 64;
      // rect
      const roadRect = roadDOM.getBoundingClientRect();
      roadInfo.value = {
        height: parseInt(roadRect.height) + carLength + "px",
        width: parseInt(roadRect.width) + carLength + "px",
      };

      const stopStationDOM = getCurrentInstance().ctx.$refs["stopStationRef"];
      // console.log(
      //   "roadRect",
      //   roadRect,
      //   "stopStationDOM :>> ",
      //   stopStationDOM[0].getBoundingClientRect()
      // );

      const stopPositionRect = stopStationDOM[0].getBoundingClientRect();
      // 计算需要移动到的位置, stopTop - roadTop
      stopPosition.value = {
        moveY: parseInt(stopPositionRect.top - roadRect.top + carLength) + "px",
        moveX:
          parseInt(stopPositionRect.left - roadRect.left + carLength) + "px",
      };
    });

    const isVertical = computed(() => props.direction === "vertical");

    // console.log("isVertical:", isVertical.value);

    return {
      // cars,
      stations,
      roadRef,
      roadInfo,
      stopStationRef,
      stopPosition,
      isVertical,
      color: "red",
    };
  },
};
</script>

<style scoped>
.test-text {
  color: v-bind(color);
}

/* .car {
  animation-name: move-to-stop-station;
  animation-duration: 10s;
  animation-fill-mode: forwards;
}


@keyframes move-to-stop-station {
  from {
    transform: translateY(0);
    transition-timing-function: linear;
  }

  to {
    transform: translateY(v-bind(stopPosition.moveY));
  }
} */

.car-move-y-end {
  animation-name: move-to-y-end;
  animation-duration: 10s;
  animation-fill-mode: forwards;
}

.car-move-x-end {
  animation-name: move-to-x-end;
  animation-duration: 10s;
  animation-fill-mode: forwards;
}

@keyframes move-to-y-end {
  from {
    /* transform: translateY(0); */
    /* transform-origin: center; */
  }

  to {
    transform: translateY(v-bind(roadInfo.height));
    transition-timing-function: linear;
  }
}

@keyframes move-to-x-end {
  from {
    /* transform: translateY(0); */
    /* transform-origin: center; */
  }

  to {
    transform: translateX(v-bind(roadInfo.width));
    transition-timing-function: linear;
  }
}
</style>
