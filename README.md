# Vuetable-2 Detail row editor
Details row editor meant for [Vuetable-2](https://github.com/ratiw/vuetable-2)
[See in action](https://kamilbeben.github.io/detail-row-editor/dist)

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
  onSave: (data) => {
  return new Promise((resolve) =>  {
      // Do stuff using data. Unless autoHideOnSaveOrCancel is set to false, the details row will be hidden when this promise is resolved
      resolve();
    });
  };
};
```
Name | Type | Default value | Description
--- | --- | --- | ---
autoRowUpdateAfterChange | boolean | `true` | If set to true, the parent row will be updated on every change
autoRowUpdateAfterSave | boolean | `true` | If set to true, the parent row will be updated after save
autoHideOnSaveOrCancel | boolean | `true` | If set to true, the details row will be automatically hidden after save / cancel
onSave | function | `null` | Function which will be called on **save**. It takes data as an argument and it is required to return a `Promise` object. 
onCancel | function | `null` | Function which will be called on **cancel**. It is required to return a `Promise` object.
header | Component | `null` | Header component
footer | Component | `null` | Custom footer component
showFooter | boolean | `true` | Whenever footer (buttons) should be hidden
buttonClasses | String | `''` | Classes which will be added to save / cancel buttons (separated by space)
boldLabel | boolean | `false` | Whenever field title should be bolded 

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