<template>
  <div class="page-popup" v-if="popupActive">

    <div class="popup">

      <div class="popup__close" @click="$emit('close-popup')">&times;</div>
      <div class="popup__title">Налоговый вычет</div>
      <div class="popup__subtitle">Используйте налоговый вычет чтобы погасить ипотеку досрочно. Размер налогового вычета составляет не более 13% от своего официального годового дохода.</div>

      <form class="form" @submit.prevent="submit">
        <div class="form-control">
          <label for="price">Ваша зарплата в месяц</label>
          <input type="text" :class="!isValid ? 'error': ''" placeholder="Введите данные" id="price" v-model="pay">
          <span class="error_text" v-if="!isValid">{{ errors.pay }}</span>
          <span class="error_text" v-if="!isValid">{{ errors.checkbox }}</span>
          <a href="#" @click.prevent="calc" v-if="payout.length === 0">Рассчитать</a>
          <a href="" @click.prevent="clear" v-if="payout.length > 0">Очистить</a>
        </div>

        <div class="form-checkbox" v-if="checkboxActive">
          <span class="form-title">Итого можете внести в качестве досрочных:</span>

          <div class="checkbox"  v-for="(sum, i) in payout" :key="i">
            <label><input type="checkbox" name="sum" :value="sum + ' ru ' + ' on ' + (i+1) + ' year'" v-model="checkbox">{{sum}} рублей<pre><span> в {{i+1}} год</span></pre></label>
            <hr>
          </div>
        </div>

        <div class="form-radio">
          <span class="form-title">Что уменьшаем?</span>
          <div class="form-radio__wrapper">
            <div class="radio">
              <input type="radio" name="radio" value="payment" checked="checked" id="radio1" v-model="paymentOrTime">
              <label for="radio1">Платёж</label>
            </div>

            <div class="radio">
              <input type="radio" name="radio" value="time" id="radio2" v-model="paymentOrTime">
              <label for="radio2">Срок</label>
            </div>
          </div>
        </div>

        <button class="button" type="submit" @click="validate">Добавить</button>
      </form>
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue'

export default {
  emits: ['close-popup'],
  props: ['popup-active'],
  setup () {
    const checkboxActive = ref(false)
    const pay = ref(null)
    const checkbox = ref([])
    const paymentOrTime = ref('payment')
    const summ = ref(260000)
    const payout = ref([])
    const isValid = ref(true)
    const errors = ref({
      pay: null,
      checkbox: null
    })

    const payYear = () => {
      if (pay.value >= 12792) {
        return Math.round((pay.value * 12) * 0.13)
      } return false
    }

    const calc = () => {
      if (payYear() && payYear() < summ.value) {
        summ.value = summ.value - payYear()
        payout.value.push(payYear())
        calc()
        checkboxActive.value = true
      } else if (payYear() > summ.value) {
        payout.value.push(Math.round(summ.value))
        checkboxActive.value = true
        summ.value = 260000
      } else if (payYear() > summ.value && summ.value > 0) {
        payout.value.push(summ.value)
        checkboxActive.value = true
      } else if (!payYear()) {
        isValid.value = false
        errors.value.pay = 'Сумма не может быть меньше 12792'
      }
    }

    const clear = () => {
      payout.value = []
      pay.value = null
      checkboxActive.value = false
      checkbox.value = []
      errors.value.checkbox = null
      errors.value.pay = null
    }

    const submit = () => {
      if (isValid.value) {
        console.group('Form Data')
        console.log('Pay', pay.value)
        console.log('Checkbox', checkbox.value)
        console.log('Radio Button', paymentOrTime.value)
        console.groupEnd()
        alert('Данные отправлены!')
      }
    }
    const validate = () => {
      if (payYear() && checkbox.value.length > 0) {
        isValid.value = true
      } else if (!pay.value && isValid.value === true) {
        errors.value.checkbox = null
        errors.value.pay = 'Поле обязательно для заполнения'
        isValid.value = false
      } else if (payYear() && checkbox.value.length === 0 && isValid.value === true) {
        errors.value.pay = null
        errors.value.checkbox = 'Пожалуйста выберите взнос'
        isValid.value = false
      } else if (!payYear()) {
        isValid.value = false
        errors.value.pay = 'Сумма не может быть меньше 12792'
      }
    }

    watch(pay, (value) => {
      if (value) {
        isValid.value = true
      }
    })

    return {
      pay,
      checkbox,
      paymentOrTime,
      payout,
      checkboxActive,
      calc,
      clear,
      submit,
      errors,
      isValid,
      validate
    }
  }
}
</script>

<style>

</style>
