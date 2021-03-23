<template>
  <div class="PaymentText border relative border-t-0">
    <div class="absolute font-bold text-xs left-2 top-1" :class="{'text-red-500': error}">{{ label }}</div>
    <input type="text" class="p-2 pt-6 w-full" :value="value" @input="$emit('input', $event.target.value)" />
    <div v-if="error" class="bg-red-100 px-2 py-1 font-bold text-xs text-red-500">{{ error }}</div>
  </div>
</template>

<script>
export default {
  props: {
    label: {
      type: String,
      default: ''
    },
    error: {
      type: String,
      default: ''
    },
    value: {
      type: String,
      default: ''
    }
  }
}
</script>