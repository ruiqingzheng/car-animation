<script>
import { reactive, ref, onMounted, getCurrentInstance } from "vue";
import { v4 as uuid } from "uuid";
export default {
  setup() {
    const cars = reactive([]);
    const roadRef = ref(null);
    const stopStationRef = ref(null);
    const roadInfo = ref({});
    const stopPosition = ref({});
    let carCounter = 0;
    const createCar = () => {
      const car = {
        id: uuid(),
        carCounter,
      };

      cars.push(car);
    };

    const padString = (str, len = 3) => {
      if (str.toString().length >= len) return str.toString();
      const _length = len - str.toString().length;
      return Array(_length).fill(0).join("") + str.toString();
    };

    const stopStationIndex = 0;
    // 创建 stations
    const stations = new Array(11).fill(0).map((_, index) => {
      return {
        stationIndex: index,
        stationName: padString(index),
        stop: index === stopStationIndex,
      };
    });

    onMounted(() => {
      const roadDOM = roadRef.value;
      // rect
      const roadRect = roadDOM.getBoundingClientRect();
      roadInfo.value = {
        height: roadRect.height + "px",
        width: roadRect.width + "px",
      };

      const stopStationDOM = getCurrentInstance().ctx.$refs["stopStationRef"];
      console.log(
        "roadRect",
        roadRect,
        "stopStationDOM :>> ",
        stopStationDOM[0].getBoundingClientRect()
      );

      const stopPositionRect = stopStationDOM[0].getBoundingClientRect();
      // 计算需要移动到的位置, stopTop - roadTop
      stopPosition.value = {
        moveY: parseInt(stopPositionRect.top - roadRect.top) + "px",
      };
    });

    return {
      cars,
      stations,
      createCar,
      roadRef,
      roadInfo,
      stopStationRef,
      stopPosition,
      color: "red",
    };
  },
};
</script>

<template>
  <div
    class="h-screen w-screen bg-gray-100 text-white font-bold flex flex-col justify-start items-center"
  >
    <div class="w-full flex justify-center items-center mt-12">
      <button
        class="border rounded hover:bg-green-500 active:bg-green-700 bg-green-600 py-2 px-6"
        @click="() => createCar()"
      >
        发车
      </button>
    </div>
    <!-- container road -->
    <div id="road" ref="roadRef" class="w-40 h-auto bg-sky-800 mt-10 relative">
      <!-- the start block -->
      <div class="w-full h-20 border-red-500 grid place-items-center">
        start block
      </div>

      <div
        class="car test-text absolute top-0 right-8 opacity-90 rounded w-10 h-16 border border-yellow-400 bg-yellow-500 grid place-items-center"
      >
        car
      </div>
      <!-- stations  -->
      <div
        v-for="station in stations"
        :key="station.stationIndex"
        :class="[
          `${station.stop ? 'bg-orange-600' : ' '}`,
          'w-full h-14 border-yellow-300 flex justify-start items-center pl-5',
        ]"
      >
        <div
          v-if="station.stop"
          ref="stopStationRef"
          class="w-0 h-0 -mt-14"
        ></div>
        {{ station.stationName }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.test-text {
  color: v-bind(color);
}

.car {
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
}

@keyframes move-to-end {
  from {
    /* transform: translateY(0); */
    /* transform-origin: center; */
  }
  to {
    transform: translateY(v-bind(roadInfo.height));
  }
}
</style>
