<script>
import { reactive, onMounted, ref } from "vue";
import { v4 as uuid } from "uuid";
import CarRoad from "./components/CarRoad.vue";
export default {
  components: {
    CarRoad,
  },
  setup() {
    const cars = reactive([]);
    const direction = ref("vertical");
    const stopStationIndex = ref(3);
    let carCounter = 0;
    const createCar = () => {
      carCounter++;
      const car = {
        id: uuid(),
        carCounter,
      };

      cars.push(car);
    };

    // createCar();

    onMounted(() => {});

    return {
      cars,
      createCar,
      direction,
      stopStationIndex,
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
    <div class="w-full flex justify-center gap-3">
      <CarRoad
        :cars="cars"
        direction="row"
        :stopStationIndex="stopStationIndex"
      ></CarRoad>
      <CarRoad
        :cars="cars"
        :direction="direction"
        :stopStationIndex="0"
      ></CarRoad>
    </div>
  </div>
</template>

<style scoped></style>
