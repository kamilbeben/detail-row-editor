// This file presents absolutely minimum which is needed to create an custom input component
<template>
  <!-- In order for this component to work properly, it must call $parent.onInputChange() method. -->
  <!-- field.value must already be updated. -->
  <select v-model="field.value" @change="$parent.onInputChange(field)">
    <option value="M">Male</option>
    <option value="F">Female</option>
    <option value="AH-64">Boeing AH-64 Apache</option>
  </select>
</template>

<script>
export default {
  props: {
    field: {
      type: Object,
      required: true
    }
  }
}
</script>
