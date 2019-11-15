<template>
    <div class="card-input">
        <label for="cardName" class="card-input__label">Card Name</label>
        <input
          type="text"
          :id="cardType"
          @input="changeName"
          class="card-input__input"
          :value="cardName"
          data-card-field
          autocomplete="off"
        />
      </div>
</template>
<script>
export default {
  name: 'CardNameInput',
  props: {
    cardType: {
      type: String,
      required: true
    },
    cardName: {
      type: String,
      required: true
    }
  },
  methods: {
    changeName (e) {
      this.$emit('input', e.target.value)
    }
  }
}
</script>
