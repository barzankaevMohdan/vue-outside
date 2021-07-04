<template>
  <div class="page-start"  v-if="!popupActive">
    <button class="button-start" @click="popupActive = true">Налоговый вычет</button>
  </div>
  <the-pop-up
    :popup-active="popupActive"
    @close-popup="popupActive = false"
  ></the-pop-up>
</template>

<script>
import { ref } from 'vue'
import ThePopUp from './components/ThePopUp'

export default {
  setup () {
    const popupActive = ref(false)

    return {
      popupActive
    }
  },
  components: { ThePopUp }
}
</script>

<style>

</style>
