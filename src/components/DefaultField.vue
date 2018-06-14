<template>
  <div>
    <div class="ui input" :class="classes">
      <input :type="field.type ? field.type : 'text'" @click="onChange" @change="onChange" @keyup="onChange" v-model="field.value"/>
    </div>
    <div v-if="errorMsg" class="error-msg"> {{ errorMsg }} </div> 
  </div>
</template>

<script>
export default {
  props: {
    field: {
      type: Object,
      required: true
    },
    rowIndex: {
      type: Number,
      required: true
    }
  },
  data () {
    return {
      classes: '',
      errorMsg: null
    }
  },
  methods: {
    onChange() {
      this.$events.fire('detail-row-field-value-changed', {
        data: this.field,
        rowIndex: this.rowIndex
      });
    }
  },
  mounted () {
    this.classes = '';
    this.errorMsg = null;
    this.$events.$on('detail-row-validation-error', function (event) {
      if (event.rowIndex !== this.rowIndex ||
          event.error.fieldName !== this.field.name) {
        return;
      }
      this.classes = 'error';
      this.errorMsg = event.error.msg;
    }.bind(this));
  }
}
</script>
