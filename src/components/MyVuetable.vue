<template>
  <vuetable ref="vuetable"
    api-url="https://vuetable.ratiw.net/api/users"
    :fields="fields"
    detail-row-component="detail-row"
    @vuetable:cell-clicked="onCellClicked"
  ></vuetable>
</template>

<script>
import Vue from 'vue'
import Vuetable from 'vuetable-2/src/components/Vuetable'

// This is me
import DetailRowEditor from './DetailRowEditor'

// Custom gender-picker field component
import GenderPicker from './GenderPicker'

Vue.component('detail-row', DetailRowEditor);

const onSave = (data) => {
  return new Promise((resolve) =>  {
      console.log('onSave.start', data);
      // Do stuff using data. Unless autoHideOnSaveOrCancel is set to false, the details row will be hidden when this promise is resolved
      setTimeout(() => {
        console.log('onSave.end'); 
        resolve(); 
      }, 1000);
    });
};

// Detal row options
Vue.prototype.detailRowOptions = {
  buttonClasses: 'mini',
  onSave: onSave,
  autoRowUpdateAfterChange: false
};

export default {
  components: {
    Vuetable
  },
  data () {
    return {
      // Field name's with dots like address.line1 or address.line2 are not currently supported
      fields: [{
          key: 1,
          name: 'name',
          title: 'Name'
        }, {
          key: 2,
          name: 'email',
          title: 'Email'
        }, {
          key: 3,
          name: 'nickname',
          title: 'Nickname',
          type: 'text' // This is the default field type, you don't need to specify that.
        }, {
          key: 4,
          name: 'age',
          title: 'Age',
          type: 'number' // Any valid HTML input type will do (note that no additional attributes will be added, so there is no point in specifying a radio type for example)
        }, {
          key: 5,
          name: 'gender',
          title: 'Gender',
          customComponent: Vue.component('gender-picker', GenderPicker) // Custom component. See GenderPicker.vue
        }]
    }
  },
  methods: {
    onCellClicked (data, field, event) {
      this.$refs.vuetable.toggleDetailRow(data.id)
    }
  }
}
</script>