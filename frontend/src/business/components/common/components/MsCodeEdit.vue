<template>
  <editor v-model="formatData" :lang="mode" @init="editorInit" theme="chrome"/>
</template>

<script>
    export default {
      name: "MsCodeEdit",
      components: { editor: require('vue2-ace-editor')},
      data() {
        return {
          formatData: ''
        }
      },
      props: {
        data: {
          type: String
        },
        init: {
          type: Function
        },
        readOnly: {
          type: Boolean,
          default() {
            return false;
          }
        },
        mode: {
          type: String,
          default() {
            return 'text';
          }
        },
        modes: {
          type: Array,
          default() {
            return ['text', 'json', 'xml', 'html'];
          }
        }
      },
      mounted() {
        this.format();
      },
      watch: {
        formatData() {
          this.$emit('update:data', this.formatData);
        },
        mode() {
          this.format();
        }
      },
      methods: {
        editorInit: function (editor) {
          require('brace/ext/language_tools') //language extension prerequsite...
          this.modes.forEach(mode => {
            require('brace/mode/' + mode); //language
          });
          require('brace/theme/chrome')
          require('brace/snippets/javascript') //snippet
          if (this.readOnly) {
            editor.setReadOnly(true);
          }
          if (this.init) {
            this.init(editor);
          }
        },
        format() {
          if (this.mode === 'json') {
            try {
              this.formatData = JSON.stringify(JSON.parse(this.data), null, '\t');
            } catch (e) {
              if (this.data) {
                this.formatData = this.data;
              }
            }
          } else {
            if (this.data) {
              this.formatData = this.data;
            }
          }
        }
      }
    }
</script>

<style scoped>

</style>
