# Vuetable-2 Detail row editor
Details row editor meant for [Vuetable-2](https://github.com/ratiw/vuetable-2)

# Usage
## Inside template
```html
<vuetable ref="vuetable"
    ...
    detail-row-component="detail-row"
    ...
  ></vuetable>
```
## Inside script
```javascript
import DetailRowEditor from 'detail-row-editor/src/components/DetailRowEditor'
Vue.component('detail-row', DetailRowEditor);
```
# Properties
Due to the fact that vuetable passes only two props to it's details row (rowData and rowIndex) any other properties needs to be passed this way
```javascript
Vue.prototype.detailRowOptions = {
  onSave: (rowData) => {
    // Create an Ajax to server, display save success message or whatever else you need to do.
  }
};
```
Name | Type | Default value | Description
--- | --- | --- | ---
buttonClasses | String | `''` | Classes which will be added to save / cancel buttons (separated by space)
boldLabel | boolean | `false` | Whenever field title should be bolded 
header | Component | `null` | Header component
hideFooter | boolean | `false` | Whenever footer (buttons) should be hidden
customFooter | Component | `null` | Custom footer component
onSave | function | `null` | Function which will be called on **save** button click. It takes rowData as the first argument
onCancel | function | `null` | Function which will be called on **cancel** button click. It takes rowData as the first argument
preventHidingOnSaveCancel | boolean | `false` | If set to true, the details row will not automaticly hide itself on save / cancel
doNotUpdateRowUnlessSaveSelected | boolean | `false` | If set to true, the parent row will not be updated on every change, only on save

# Example code
``` bash
# clone project
git clone https://github.com/kamilbeben/detail-row-editor.git
cd detail-row-editor

# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev
```