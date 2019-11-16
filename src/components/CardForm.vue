<template>
  <div class="card-form">
    <div class="card-list">
      <card-display
        :fields="fields"
        :labels="formData"
        :is-card-number-masked="isCardNumberMasked"
        :randomBackgrounds="randomBackgrounds"
        :backgroundImage="backgroundImage"
      ></card-display>
    </div>
    <div class="card-form__inner">
      <card-number-input
        :card-type="fields.cardNumber"
        :is-card-number-masked="isCardNumberMasked"
        v-model="formData.cardNumber"
        @toggle-mask-number="toggleMaskNumber"
      >
      </card-number-input>
      <card-name-input
        :card-type="fields.cardName"
        v-model="formData.cardName"
      >
      </card-name-input>
      <div class="card-form__row">
        <div class="card-form__col">
          <div class="card-form__group">
            <label for="cardMonth" class="card-input__label">Expiration Date</label>
            <select
              class="card-input__input -select"
              :id="fields.cardMonth"
              v-model="formData.cardMonth"
              @change="changeMonth"
              data-card-field
            >
              <option value disabled selected>Month</option>
              <option
                v-bind:value="n < 10 ? '0' + n : n"
                v-for="n in 12"
                v-bind:disabled="n < minCardMonth"
                v-bind:key="n"
              >{{generateMonthValue(n)}}</option>
            </select>
            <select
              class="card-input__input -select"
              :id="fields.cardYear"
              v-model="formData.cardYear"
              @change="changeYear"
              data-card-field
            >
              <option value disabled selected>Year</option>
              <option
                v-bind:value="$index + minCardYear"
                v-for="(n, $index) in 12"
                v-bind:key="n"
              >{{$index + minCardYear}}</option>
            </select>
          </div>
        </div>
        <div class="card-form__col -cvv">
          <div class="card-input">
            <label for="cardCvv" class="card-input__label">CVV</label>
            <input
              type="tel"
              class="card-input__input"
              v-number-only
              :id="fields.cardCvv"
              maxlength="4"
              :value="formData.cardCvv"
              @input="changeCvv"
              data-card-field
              autocomplete="off"
            />
          </div>
        </div>
      </div>

      <button class="card-form__button" v-on:click="invaildCard">Submit</button>
    </div>
  </div>
</template>

<script>
import Card from '@/components/Card'
import CardNameInput from './input_forms/CardNameInput'
import CardNumberInput from './input_forms/CardNumberInput'

export default {
  name: 'CardForm',
  components: {
    'card-display': Card,
    'card-name-input': CardNameInput,
    'card-number-input': CardNumberInput
  },
  props: {
    formData: {
      type: Object,
      default: () => {
        return {
          cardName: '',
          cardNumber: '',
          cardMonth: '',
          cardYear: '',
          cardCvv: ''
        }
      }
    },
    backgroundImage: [String, Object],
    randomBackgrounds: {
      type: Boolean,
      default: true
    }
  },
  directives: {
    'number-only': {
      bind (el) {
        function checkValue (event) {
          event.target.value = event.target.value.replace(/[^0-9]/g, '')
          if (event.charCode >= 48 && event.charCode <= 57) {
            return true
          }
          event.preventDefault()
        }
        el.addEventListener('keypress', checkValue)
      }
    }
  },
  data () {
    return {
      fields: {
        cardNumber: 'v-card-number',
        cardName: 'v-card-name',
        cardMonth: 'v-card-month',
        cardYear: 'v-card-year',
        cardCvv: 'v-card-cvv'
      },
      minCardYear: new Date().getFullYear(),
      isCardNumberMasked: true,
      mainCardNumber: this.cardNumber,
      cardNumberMaxLength: 19
    }
  },
  computed: {
    minCardMonth () {
      if (this.formData.cardYear === this.minCardYear) return new Date().getMonth() + 1
      return 1
    }
  },
  watch: {
    cardYear () {
      if (this.formData.cardMonth < this.minCardMonth) {
        this.formData.cardMonth = ''
      }
    }
  },
  methods: {
    toggleMaskNumber (value) {
      this.isCardNumberMasked = value
    },
    generateMonthValue (n) {
      return n < 10 ? `0${n}` : n
    },
    changeMonth () {
      this.$emit('input-card-month', this.formData.cardMonth)
    },
    changeYear () {
      this.$emit('input-card-year', this.formData.cardYear)
    },
    changeCvv (e) {
      this.formData.cardCvv = e.target.value
      this.$emit('input-card-cvv', this.formData.cardCvv)
    },
    invaildCard () {
      let number = this.formData.cardNumber
      let sum = 0
      let isOdd = true
      for (let i = number.length - 1; i >= 0; i--) {
        let num = number.charAt(i)
        if (isOdd) {
          sum += num
        } else {
          num = num * 2
          if (num > 9) {
            num = num.toString().split('').join('+')
          }
          sum += num
        }
        isOdd = !isOdd
      }
      if (sum % 10 !== 0) {
        alert('invaild card number')
      }
    }
  }
}
</script>
