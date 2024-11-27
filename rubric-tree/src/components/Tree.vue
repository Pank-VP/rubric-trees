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
          <span v-if="allowEmpty">Больше рубрик</span>
          <span v-else>Меньше рубрик</span>
        </button>
      </div>

    </div>
    <div v-if="newData.length" class="card">
<!--        <div class="flex items-center">-->
<!--          <div v-if="item.children.length" @click="item.showSection = !item.showSection"-->
<!--               class="rounded-full hover:bg-slate-500 size-5 flex justify-center items-center"-->
<!--               :class="{'rotate-90': item.showSection}"-->
<!--          >-->
<!--            <i class="pi pi-chevron-right" style="font-size: 12px"></i>-->
<!--          </div>-->
<!--          <div v-else class="size-5"></div>-->
<!--          <input v-model="item.value" :id="item.id" type="checkbox" :value="true"-->
<!--                 @change="sumSelectedCounts(item.count, item.value)" class="mr-2">-->
<!--          <a :href="item.url" class="hover:text-white hover:underline">-->
<!--            <p>-->
<!--              {{ item.title }}-->
<!--            </p>-->
<!--          </a>-->
<!--        </div>-->
<!--        <div v-if="item.children.length && item.showSection">-->
<!--          <div v-for="(i, index) in item.children"-->
<!--               :key="index + new Date().getTime()"-->
<!--               class="ml-10"-->
<!--          >-->
<!--            <div class="flex items-start">-->
<!--              <div v-if="i?.children" @click="i.showSection = !i.showSection"-->
<!--                   :class="{'rotate-90': i.showSection}">-->
<!--                <i class="pi pi-chevron-right" style="font-size: 12px"></i>-->
<!--              </div>-->
<!--              <input v-model="i.value" :id="i.id" type="checkbox" :value="true"-->
<!--                     @change="sumSelectedCounts(i.count, i.value)" class="mx-2 mt-[5px]">-->
<!--              <a :href="i.url" class="text-start">-->
<!--                <p class="inline-block w-full max-w-[300px]">-->
<!--                  {{ i.title }}-->
<!--                </p>-->
<!--              </a>-->
<!--            </div>-->
<!--            <div v-if="i.children.length && i.showSection">-->
<!--              <div v-for="(e, index) in i.children"-->
<!--                   :key="index + new Date().getTime()"-->
<!--                   class="ml-10"-->
<!--              >-->
<!--                <div class="flex items-start">-->
<!--                  <div v-if="e?.children.length" @click="e.showSection = !e.showSection"-->
<!--                       :class="{'rotate-90': e.showSection}">-->
<!--                    <i class="pi pi-chevron-right" style="font-size: 12px"></i>-->
<!--                  </div>-->
<!--                  <input v-model="e.value" :id="e.id" type="checkbox" :value="true"-->
<!--                         @change="sumSelectedCounts(e.count, e.value)" class="mx-2 mt-[5px]">-->
<!--                  <a :href="e.url" class="text-start">-->
<!--                    <p class="inline-block w-full max-w-[300px]">-->
<!--                      {{ e.title }}-->
<!--                    </p>-->
<!--                  </a>-->
<!--                </div>-->
<!--              </div>-->
<!--            </div>-->
<!--          </div>-->
<!--        </div>-->
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
import Tree from 'primevue/tree';
import NestedComponent from "./NestedComponent.vue";

const nodes = ref(null);
const newData = ref([])
const allowEmpty = ref(1);
const count = ref(0)

const getData = async () => {
  try {
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
