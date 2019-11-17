<template>
    <div class="card-input">
        <label for="cardNumber" class="card-input__label">Card Number</label>
        <input
          v-number-only
          type="tel"
          :id="cardType"
          @input="changeNumber"
          @focus="focusCardNumber"
          @blur="blurCardNumber"
          class="card-input__input"
          :value="value"
          :maxlength="cardNumberMaxLength"
          data-card-field
          autocomplete="off"
        />
        <button
          class="card-input__eye"
          :class="{ '-active' : !isCardNumberMasked }"
          title="Show/Hide card number"
          tabindex="-1"
          :disabled="value === ''"
          @click="toggleMask"
        ></button>
      </div>
</template>
<script>
export default {
  name: 'CardNumberInput',
  props: {
    cardType: {
      type: String,
      required: true
    },
    value: {
      type: String,
      required: true
    },
    isCardNumberMasked: {
      type: Boolean
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
  mounted () {
    this.maskCardNumber()
  },
  data () {
    return {
      cardNumberMaxLength: 19,
      mainCardNumber: this.value
    }
  },
  methods: {
    changeNumber (e) {
      this.cardNumberRegex(e.target.value)
    },
    blurCardNumber () {
      if (this.isCardNumberMasked) {
        this.maskCardNumber()
      }
    },
    toggleMask () {
      this.$emit('toggle-mask-number', !this.isCardNumberMasked)
      if (this.isCardNumberMasked) {
        this.maskCardNumber()
      } else {
        this.unMaskCardNumber()
      }
    },
    maskCardNumber () {
      this.mainCardNumber = this.value
      let arr = this.value.split('')
      arr.forEach((element, index) => {
        if (index > 4 && index < 14 && element.trim() !== '') {
          arr[index] = '*'
        }
      })
      this.$emit('input', arr.join(''))
    },
    unMaskCardNumber () {
      this.$emit('input', this.mainCardNumber)
    },
    focusCardNumber () {
      this.unMaskCardNumber()
    },
    cardNumberRegex (numValue) {
      this.$emit('input', numValue)
      let value = this.value.replace(/\D/g, '')
      // american express, 15 digits
      if ((/^3[47]\d{0,13}$/).test(value)) {
        console.log('first condition')
        this.$emit('input', value.replace(/(\d{4})/, '$1 ').replace(/(\d{4}) (\d{6})/, '$1 $2 '))
        this.cardNumberMaxLength = 17
      } else if ((/^3(?:0[0-5]|[68]\d)\d{0,11}$/).test(value)) { // diner's club, 14 digits
        console.log('second condition')
        this.$emit('input', value.replace(/(\d{4})/, '$1 ').replace(/(\d{4}) (\d{6})/, '$1 $2 '))
        this.cardNumberMaxLength = 16
      } else if ((/^\d{0,16}$/).test(value)) { // regular cc number, 16 digits
        console.log('third condition')
        this.$emit('input', value.replace(/(\d{4})/, '$1 ').replace(/(\d{4}) (\d{4})/, '$1 $2 ').replace(/(\d{4}) (\d{4}) (\d{4})/, '$1 $2 $3 '))
        this.cardNumberMaxLength = 19
      }
    }
  }
}
</script>
