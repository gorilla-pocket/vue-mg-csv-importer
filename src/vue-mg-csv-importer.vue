<template>
<div class="input-group">
    <input type="file" name="files[]" style="display:none" @change="fileChange" ref="file"/>
    <input type="text" class="form-control" placeholder="" readonly="readonly" :value="file_name" @click="$refs.file.click()">
    <div class="input-group-append">
        <button type="button" class="btn btn-primary" @click="$refs.file.click()">選択...</button>
    </div>
</div>
</template>

<script>
import encoding from 'encoding-japanese'
export default {
    props: {
        file_name: {
            default: '',
        },
        is_header: {
            default: false,
        },
        delim: {
            default: ',',
        },
        file_character_code: {
            default: '',
        },
        is_header_detect: {
            type: Boolean,
            default: false,
        } 
    },
    data: function() {
        return {
            workers: []
        }
    },
    methods: {
        fileChange: function (e) {
            if (!e.target.files.length) {
                this.$emit('update:file_name', '')
                this.$emit('update:file_character_code', '')
                this.workers = []
                this.$emit('file-change', this.workers)
                return
            }
            let file = e.target.files[0]
            this.$emit('update:file_name', file.name)
            const reader = new FileReader()
            const workers = []

            const loadFunc = () => {
                var codes = new Uint8Array(reader.result);
                var header_index = codes.indexOf(10) + 1
                var header_detected = encoding.detect(codes.slice(0,header_index));
                var detected = encoding.detect(codes);
                if (this.is_header_detect && header_detected != detected) detected = header_detected
                this.$emit('update:file_character_code', detected)
                var unicodeString = encoding.convert(codes, {
                    to: 'unicode',
                    from: detected,
                    type: 'string'
                });
                const lines = this.parseCSV2(unicodeString.trim(),this.delim)
                lines.forEach((element, index) => {
                    const workerData = element
                    let worker = {}
                    if (this.is_header && index == 0) return
                    for (let i = 0; i < workerData.length; i++) {
                        worker['col'+(i+1)] = workerData[i]
                    }
                    workers.push(worker)
                });
                
                this.workers = workers

                this.$emit('file-change', this.workers)
            };
            reader.onload = loadFunc
            reader.readAsArrayBuffer(file)
        },
        parseCSV2: function (text, delim) {
            if (!delim) delim = ','
            const tokenizer = new RegExp(delim + '|\r?\n|[^' + delim + '"\r\n][^' + delim + '\r\n]*|"(?:[^"]|"")*"', 'g')

            let record = 0, field = 0, data = [['']], qq = /""/g
            text.replace(/\r?\n$/, '').replace(tokenizer, function(token) {
                switch (token) {
                    case delim:
                        data[record][++field] = ''
                        break
                    case '\n': case '\r\n':
                        data[++record] = ['']
                        field = 0
                        break
                    default:
                        data[record][field] = (token.charAt(0) != '"') ? token : token.slice(1, -1).replace(qq, '"')
                }
            })

            return data
        }
    },
}
</script>
