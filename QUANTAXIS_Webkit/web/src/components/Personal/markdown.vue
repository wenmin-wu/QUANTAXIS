<template>
  <div>
    <mu-paper>
      <mu-text-field hintText="Input One" class="demo-divider-form" :underlineShow="false" v-model="title" />
    </mu-paper>
    <mu-popup position="top" :overlay="false" popupClass="demo-popup-top" :open="topPopup">
      保存成功
    </mu-popup>
    <markdown-editor id='markdown' :highlight="true" v-model="content" ref="markdownEditor" :configs="configs"></markdown-editor>
    <div>
      <mu-flat-button v-on:click='save()' label="SAVE" class="demo-flat-button" />

      <mu-flat-button v-on:click='clear()' label="CLEAR" class="demo-flat-button" />
    </div>
  </div>
</template>

<script>
  import hljs from 'highlight.js';
  import axios from 'axios'

  window.hljs = hljs;
  import markdownEditor from 'vue-simplemde/src/markdown-editor'

  // 基础用法
  export default {
    components: {
      markdownEditor
    },
    data() {
      return {
        content: '# QUANTAXIS Markdown Editor',
        title: '',
        topPopup: false,
        configs: {
          spellChecker: false // 禁用拼写检查
        }
      }
    },
    methods: {
      clear() {
        this.content = ''
      },
      ready(id) {
        axios.get('http://localhost:3000/notebook/query?id=' + id)
          .then(response => {
            this.title = response.data['title']
            this.content = response.data['content']
            console.log(response.data)
          })


      },
      handleChange(value) {
        this.value = value
      },
      open(position) {
        this[position + 'Popup'] = true
        sleep(2)
        this[position + 'Popup'] = false

        //this[position + 'Popup'] = false
      },
      close(position) {
        this[position + 'Popup'] = false
      },

      save() {
        console.log(this.content)
        let text = String(this.content)
        axios.get('http://localhost:3000/notebook/modify', {
            params: {
              id: this.$route.params.id,
              title: this.title,
              content: text

            }
          })
          .then(response => {

            //this.title = response.data['value']['title']
            //this.content = response.data['value']['content']
            this.open('top')
            //this.close('top')
          })
      }
    },


    mounted() {
      this.$nextTick(function () {
        this.ready(this.$route.params.id);

      })
    },
    watch: {
      topPopup(val) {
        if (val) {
          setTimeout(() => {
            this.topPopup = false
          }, 800)
        }
      },
      content: function (val, oldVal) {
        setTimeout(() => {
        this.save()
      }, 800)
      }
    }


  }

</script>

<style>
  @import '~simplemde-theme-base/dist/simplemde-theme-base.min.css';
  @import '~github-markdown-css';
  @import '~highlight.js/styles/atom-one-dark.css';
  @import '~simplemde/dist/simplemde.min.css';


  .CodeMirror-sizer {
    text-align: left;
    position: relative;
  }

  .CodeMirror-scroll {
    text-align: left;
    position: relative;
  }

  .markdown-editor {
    text-align: left;
    text-overflow: scrollable;
    position: relative;
  }
  /* 高亮主题可选列表：https://github.com/isagalaev/highlight.js/tree/master/src/styles */

  .demo-divider-form {
    margin-bottom: 0;
    text-align: left;
    font-size: 2em;
    margin: .67em 0;
    font-weight: bold;
    font-family: 'Segoe UI', 'Tahoma', 'Geneva', 'Verdana', 'sans-serif';
    margin-left: 20px;
  }

  .demo-popup-top {
    width: 100%;
    opacity: .8;
    height: 48px;
    line-height: 48px;
    display: flex;
    align-items: center;
    justify-content: center;
    max-width: 375px;
  }

</style>
