<template>
  <div v-for="(item, index) in props.items" :key="index + new Date().getTime()" class="ml-10">
    <div class="flex items-center">
      <div v-if="isShowChevron(item)" @click="item.showSection = !item.showSection"
           class="rounded-full hover:bg-slate-500 size-5 flex justify-center items-center"
           :class="{'rotate-90': item.showSection}"
      >
        <i class="pi pi-chevron-right" style="font-size: 12px"></i>
      </div>
      <div v-else class="size-5"></div>
      <input v-model="item.value" :id="item.id" type="checkbox" :value="true"
             @change="changeData(item.count, item.value)" class="mr-2">
      <a :href="item.url" class="hover:text-white hover:underline">
        <p>
          {{ item.title }}
        </p>
      </a>
    </div>
    <NestedComponent
        v-if="item.children && item.showSection"
        :items="item.children"
        @change-data="changeData"
    />
  </div>
</template>
<script setup>
//imports
import NestedComponent from "./NestedComponent.vue";

const props = defineProps({
  items: {
    type: Array,
  }
})
const emit = defineEmits(['changeData'])
const changeData = (count, value) => {
  setTimeout(() => {
    emit('changeData', count, value)
  },50)
}
const isShowChevron = (item) => {
  return item.children && item.children.length
}
</script>
