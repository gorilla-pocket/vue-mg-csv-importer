# vue-mg-csv-importer
vue-mg-csv-importer

![npm](https://img.shields.io/npm/v/vue-mg-csv-importer)
![npm](https://img.shields.io/npm/dm/vue-mg-csv-importer)

## Installation

```
npm i vue-mg-csv-importer
```

## Usage

app.js

```javascript
import CsvImporter from 'vue-mg-csv-importer'
Vue.component('CsvImporter', CsvImporter)
```

Example:

```html
<template>
  <section class="container mt-2">
    <div class="form-group form-check">
      <input type="checkbox" class="form-check-input" id="header" v-model="checked">
      <label class="form-check-label" for="header">ヘッダーあり</label>
    </div>
    <csv-importer @file-change="data=$event" :file_name.sync="file_name" :is_header="checked"/>
    <div>
      data: {{data}}
    </div>
    <div>
      filename: {{file_name}}
    </div>
  </section>
</template>

<script>
import CsvImporter from '../src/vue-mg-csv-importer'
import 'bootstrap/dist/css/bootstrap.min.css'
export default {
  data() {
    return {
      data: [],
      file_name: '',
      checked: false,
    }
  },
    methods: {
      //
    },
  components: {
    CsvImporter,
  },  
}
</script>
```

## License

MIT
