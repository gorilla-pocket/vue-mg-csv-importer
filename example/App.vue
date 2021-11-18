<template>
  <section class="container mt-2">
    <div class="form-group form-check">
      <input type="checkbox" class="form-check-input" id="header" v-model="checked">
      <label class="form-check-label" for="header">ヘッダーあり</label>
    </div>
    <csv-importer @file-change="data=$event" :file_name.sync="file_name" :file_character_code.sync="file_character_code" :is_header_detect="true" :is_header="checked" :delim="','"/>
    <div>
      data: {{data}}
    </div>
    <div>
      filename: {{file_name}}
    </div>
    <div>
      file character code: {{file_character_code}}
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
      file_character_code: '',
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