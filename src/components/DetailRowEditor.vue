<template>
  <div class="details-row-container">
    <component v-if="options.header" :is="options.header"></component>
    <!-- fields container -->
    <div class="ui two column grid form">
      <div v-for="field in $fields" :key="field.key" class="row field">
        <div class="two wide column flex">
          <label :class="options.boldLabel ? 'bold' : ''"> {{ field.title }} </label>
        </div>
        <div class="fourteen wide column">
          <component :is="field.detailRowComponent" :field="field" :row-index="rowIndex"></component>
        </div>
      </div>
    </div>
    <!-- footer -->
    <div v-if="options.showFooter && !options.footer" class="footer-container">
      <button @click="onSave" :class="[options.buttonClasses, options.saveButtonClass]" class="ui positive button">Save</button>
      <button @click="onCancel" :class="[options.buttonClasses, options.cancelButtonClass]" class="ui negative button">Cancel</button>
    </div>
    <div v-if="options.footer" class="footer-container">
      <component v-if="options.footer" :is="options.footer"></component>
    </div>
  </div>
  </template>

  <script>

  import Vue from 'vue'
  import DefaultField from './DefaultField'

  export default {
    props: {
      rowData: {
        type: Object,
        required: true
      },
      rowIndex: {
        type: Number
      }
    },
    data () {
      return {
        $fields: [],
        options: {
          autoRowUpdateAfterChange: true,
          autoRowUpdateAfterSave: true,
          autoHideOnSaveOrCancel: true,
          validateData: null,
          onSave: null,
          onCancel: null,
          header: null,
          footer: null,
          showFooter: true,
          buttonClasses: '',
          boldLabel: false,
          saveButtonClass: '',
          cancelButtonClass: ''
        }
      };
    },
    methods: {
      printErrors (errors) {
        this.options.saveButtonClass = '';
        errors.forEach(error => {
          this.$events.fire('detail-row-validation-error', {
            error: error,
            rowIndex: this.rowIndex
          });
        })
      },
      onSaveSuccess () {
        this.autoUpdateRowData();
        this.hide();
      },
      onSave () {
        if (this.options.onSave) {
          this.options.saveButtonClass = 'loading';
          this.options.onSave(this.prepareData())
            .then(this.onSaveSuccess, this.printErrors)
        } else {
          this.hide();
        }
      },
      onCancel () {
        this.revert();
        this.$fields = this.prepareFields();
        if (this.options.onCancel) {
          this.options.cancelButtonClass = 'loading';
          this.options.onCancel()
            .then(this.hide);
        } else {
          this.hide();
        }
      },
      onInputChange (field) {
        if (this.options.autoRowUpdateAfterChange) {
          this.updateFieldData(field);
        }
      },
      autoUpdateRowData () {
        if (this.options.autoRowUpdateAfterSave) {
          this.$fields.forEach(this.updateFieldData);
        }
      },
      updateFieldData (field) {
        this.rowData[field.name] = field.value;
      },
      prepareData () {
        const data = JSON.parse(JSON.stringify(this.revertData));
        this.$fields
          .forEach((field) => {
            data[field.name] = field.value;
          });
        return data;
      },
      revert () {
        Object.keys(this.revertData)
          .forEach(key => this.rowData[key] = this.revertData[key]);
      },
      cloneFields () {
        return this.detailRowOptions.fields
          .filter(field => field.editable)
          .map(field => { 
            return {
              name: field.name,
              title: field.title,
              key: field.key,
              type: field.type,
              detailRowComponent: field.detailRowComponent || Vue.component('default-component', DefaultField)
            }
          });
      },
      prepareFields () {
        const fields = this.cloneFields();
        Object.keys(this.rowData)
          .forEach((key) => {
            const field = fields.find(field => field.name === key);
            if (field) {
              field.value = this.rowData[key];
            }
          })
        return fields;
      },
      hide () {
        this.options.saveButtonClass = '';
        this.options.cancelButtonClass = '';
        if (this.options.autoHideOnSaveOrCancel) {
          this.$events.fire('hide-detail-row', {
            index: this.rowIndex,
            data: this.rowData
          });
        }
      }
    },
    mounted () {
      // Copy options 
      if (this.detailRowOptions) {
        Object.keys(this.detailRowOptions)
          .forEach(key => this.options[key] = this.detailRowOptions[key]); 
      }
      // Clone data (for cancel button)
      this.revertData = JSON.parse(JSON.stringify(this.rowData));
      // Clone fields
      this.$fields = this.prepareFields();
      // Assign event listeners
      this.$events.$on('detail-row-field-value-changed', (event) => {
        const data = event.data;
        const index = event.rowIndex;
        if (index === this.rowIndex) {
          const field = this.$fields.find(field => field.name === data.name);
          field.value = data.value;
          this.onInputChange(field);
        }
      });
    }
  }
  </script>
  
  <style scoped>

    .details-row-container {
      padding: 0 1rem;
    }

    .grid {
      margin: 0;
      margin-bottom: 0.5rem;
    }

    .fourteen.wide.column {
      padding-right: 0;
    }

    .flex {
      display: flex !important;
    }

    .bold {
      font-weight: bold;
    }

    .column.flex > label {
      margin: auto 0;
    }

    .footer-container {
      display: flex;
      flex-direction: row-reverse;
    }

  </style>
  
  <style>
    
    /* Override default semantic ui behavior */
    .ui.selectable.table tbody tr.vuetable-detail-row:hover {
      background-color: unset !important;
    }

    .details-row-container > .ui.two.column.grid.form > .row.field {
      padding: 0.5rem 0;
      margin-bottom: 0;
    }

    .details-row-container .error input {
      background-color: #fff6f6 !important;
      border-color: #e0b4b4 !important;
      color: #9f3a38 !important;
      -webkit-box-shadow: none !important;
      box-shadow: none !important;
    }

    .error-msg {
      color: #9f3a38;
      margin-top: 0.5rem;
    }

  </style>
