<template>
    <div class="card-input">
        <label for="cardNumber" class="card-input__label">Card Number</label>
        <input
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
    }
  },
  mounted () {
    this.maskCardNumber()
  },
  data () {
    return {
      isCardNumberMasked: true,
      cardNumberMaxLength: 19,
      mainCardNumber: this.value
    }
  },
  methods: {
    changeNumber (e) {
      this.$emit('input', this.cardNumberRegex(e.target.value))
    },
    blurCardNumber () {
      if (this.isCardNumberMasked) {
        this.maskCardNumber()
      }
    },
    toggleMask () {
      this.isCardNumberMasked = !this.isCardNumberMasked
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
    // @TODO Change the name of the method to a more appropriate one
    cardNumberRegex (numValue) {
      let value = numValue.replace(/\D/g, '')

      if ((/^3[47]\d{0,13}$/).test(value)) {
        numValue = value.replace(/(\d{4})/, '$1 ').replace(/(\d{4}) (\d{6})/, '$1 $2 ')
        this.cardNumberMaxLength = 17
      } else if ((/^3(?:0[0-5]|[68]\d)\d{0,11}$/).test(value)) { // diner's club, 14 digits
        numValue = value.replace(/(\d{4})/, '$1 ').replace(/(\d{4}) (\d{6})/, '$1 $2 ')
        this.cardNumberMaxLength = 16
      } else if ((/^\d{0,16}$/).test(value)) { // regular cc number, 16 digits
        numValue = value.replace(/(\d{4})/, '$1 ').replace(/(\d{4}) (\d{4})/, '$1 $2 ').replace(/(\d{4}) (\d{4}) (\d{4})/, '$1 $2 $3 ')
        this.cardNumberMaxLength = 19
      }

      return numValue
    }
  }
}
</script>
<style lang="css" scoped>

</style>
