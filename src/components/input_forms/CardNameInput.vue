<template>
  <div class="card-input">
    <label for="cardName" class="card-input__label">Card Name</label>
    <input
      type="text"
      :id="cardField"
      v-letter-only
      @input="changeName"
      class="card-input__input"
      :value="value"
      data-card-field
      autocomplete="off"
    />
  </div>
</template>
<script>
export default {
  name: 'CardNameInput',
  props: {
    value: {
      type: String
    },
    cardField: {
      type: String
    }
  },
  directives: {
    'letter-only': {
      bind (el) {
        function checkValue (event) {
          if (event.charCode >= 48 && event.charCode <= 57) {
            event.preventDefault()
          }
          return true
        }
        el.addEventListener('keypress', checkValue)
      }
    }
  },
  methods: {
    changeName (e) {
      this.$emit('input', e.target.value)
    }
  }
}
</script>
