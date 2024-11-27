<template>
  <div class="w-screen h-full max-w-3xl bg-slate-700 text-white rounded-2xl">
    <div class="pt-5 px-8 flex w-full justify-between">
      <div>
        <span>
          Сумма: {{ count }}
        </span>
      </div>
      <div class="text-start">
        <p>
          Показать:
        </p>
        <button @click="changeParams">
          <span v-if="allowEmpty">Меньше рубрик</span>
          <span v-else>Больше рубрик</span>
        </button>
      </div>

    </div>
    <div v-if="newData.length" class="card">
      <NestedComponent :items="newData" @change-data="sumSelectedInsideElements" />
    </div>
    <div v-else class="w-full h-full min-h-[424px] flex justify-center items-center">
      <span>
        Загрузка ...
      </span>
    </div>
  </div>
</template>
<script setup>
import {ref, onMounted} from 'vue'
import NestedComponent from "./NestedComponent.vue";

const nodes = ref(null);
const newData = ref([])
const allowEmpty = ref(1);
const count = ref(0)

const getData = async () => {
  try {
    count.value = 0
    nodes.value = null
    newData.value = []
    const response = await fetch(`https://www.klerk.ru/yindex.php/v3/event/rubrics?allowEmpty=${allowEmpty.value}`)
    nodes.value = await response.json()
    newData.value = Array.from(nodes.value)
    // nodes.value.forEach(i => newData.value.push(i))
    // newData.value = nodes.value.map(i => i)
    console.log(newData.value)
    createNewData(newData.value)
  } catch (e) {
    console.log(e)
  }
}
const createNewData = (item) => {
  for (let i = 0; i < item.length; i += 1) {
    item[i].value = false
    item[i].showSection = false
    if (item[i].children) {
      createNewData(item[i].children)
    }
  }
}
const sumSelectedCounts = (value, isActive) => {
  !!isActive ? count.value += value : count.value -= value
}
const sumSelectedInsideElements = (e, i) => {
  sumSelectedCounts(e, i)
}
const changeParams = async () => {
  !!allowEmpty.value ? allowEmpty.value = 0 : allowEmpty.value = 1
  await getData()
}
onMounted(async () => {
  await getData()
})

</script>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
