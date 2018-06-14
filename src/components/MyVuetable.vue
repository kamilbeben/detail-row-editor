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
import VueEvents from 'vue-events'

// Necessary
Vue.use(VueEvents)

// This is me
import DetailRowEditor from './DetailRowEditor'

// Custom gender-picker field component
import GenderPicker from './GenderPicker'

Vue.component('detail-row', DetailRowEditor);

const validate = (data) => {
  if (Math.random(1) >= 0.5) return [];

  return [{
    fieldName: 'email',
    msg: 'Invalid format'
  }];
};

const onSave = (data) => {
  return new Promise((resolve, reject) =>  {
      const errors = validate(data);
      if (errors.length) {
        reject(errors);
      } else {
        // post data to server, etc
        resolve();
      }
    });
};

// Detal row options
Vue.prototype.detailRowOptions = {
  buttonClasses: 'mini',
  onSave: onSave,
  autoRowUpdateAfterChange: false,
  fields: []
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
          title: 'Name',
          editable: true
        }, {
          key: 2,
          name: 'email',
          title: 'Email',
          editable: true
        }, {
          key: 3,
          name: 'nickname',
          title: 'Nickname',
          type: 'text' // This is the default field type, you don't need to specify that.
        }, {
          key: 4,
          name: 'age',
          title: 'Age',
          type: 'number', // Any valid HTML input type will do (note that no additional attributes will be added, so there is no point in specifying a radio type for example)
          editable: true
        }, {
          key: 5,
          name: 'gender',
          title: 'Gender',
          detailRowComponent: Vue.component('gender-picker', GenderPicker), // Custom component. See GenderPicker.vue
          editable: true
        }]
    }
  },
  methods: {
    onCellClicked (data, field, event) {
      this.$refs.vuetable.toggleDetailRow(data.id)
    }
  },
  mounted () {
    Vue.prototype.detailRowOptions.fields = this.fields;
    this.$events.$on('hide-detail-row', ($event) => {
      this.$refs.vuetable.hideDetailRow($event.data.id)
    });
  }
}
</script>