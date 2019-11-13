<template>
    <div class="card-input">
        <label for="cardName" class="card-input__label">Card Name</label>
        <input
          type="text"
          :id="fields.cardName"
          v-letter-only
          @input="changeName"
          class="card-input__input"
          :value="formData.cardName"
          data-card-field
          autocomplete="off"
        />
      </div>
</template>
<script>
export default {
  name: 'CardName'  
}
</script>
<style lang="css" scoped>

</style>