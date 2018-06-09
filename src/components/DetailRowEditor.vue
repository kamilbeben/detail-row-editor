<template>
  <div class="details-row-container">
    <component v-if="options.header" :is="options.header"></component>
    <!-- fields container -->
    <div class="ui two column grid form">
      <div v-for="field in fields" :key="field.key" class="row field">
        <div class="two wide column flex">
          <label :class="options.boldLabel ? 'bold' : ''"> {{ field.title }} </label>
        </div>
        <div class="fourteen wide column">
          <component v-if="field.customComponent" :is="field.customComponent" :field="field"></component>
          <input v-else :type="field.type ? field.type : 'text'" @keyup="onInputChange(field)" v-model="field.value"/>
        </div>
      </div>
    </div>
    <!-- footer -->
    <div v-if="!options.hideFooter || options.customFooter" class="footer-container">
      <button @click="onSave" :class="options.buttonClasses" class="ui positive button">Save</button>
      <button @click="onCancel" :class="options.buttonClasses" class="ui negative button">Cancel</button>
    </div>
    <div v-if="options.customFooter" class="footer-container">
      <component v-if="options.customFooter" :is="options.customFooter"></component>
    </div>
  </div>
  </template>

  <script>

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
    methods: {
      onSave () {
        this.updateRowData();
        if (!this.options.preventHidingOnSaveCancel) {
          this.hide();
        }
        if (this.options.onSave) {
          this.options.onSave();
        }
      },
      onCancel () {
        this.revert();
        this.fields = this.prepareFields();
        if (!this.options.preventHidingOnSaveCancel) {
          this.hide();
        }
        if (this.options.onCancel) {
          this.options.onCancel();
        }
      },
      onInputChange (field) {
        if (!this.options.doNotUpdateRowUnlessSaveSelected) {
          this.updateFieldData(field);
        }
      },
      updateFieldData (field) {
        this.rowData[field.name] = field.value;
      },
      updateRowData () {
        this.fields
          .forEach((field) => {
            this.rowData[field.name] = field.value;
          })
      },
      revert () {
        Object.keys(this.revertData)
          .forEach(key => this.rowData[key] = this.revertData[key]);
      },
      cloneFields () {
        return this.$parent.fields
          .map(field => { 
            return {
              name: field.name,
              title: field.title,
              key: field.key,
              type: field.type,
              customComponent: field.customComponent
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
        this.$parent.hideDetailRow(this.rowIndex + 1);
      }
    },
    data () {
      return {
        fields: [],
        options: {}
      };
    },
    mounted () {
      this.options = this.detailRowOptions || {};
      this.revertData = JSON.parse(JSON.stringify(this.rowData));
      this.fields = this.prepareFields();
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

    .row.field {
      padding: 0.5rem 0;
      margin-bottom: 0;
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

  </style>