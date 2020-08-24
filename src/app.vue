<template>
  <div class="editor">
    <h2>
      Collaborative Editing
    </h2>
    <div class="message">
      With the Collaboration Extension it's possible for several users to work on a document at the same time. To make this possible, client-side and server-side code is required. This example shows this using a <a href="https://glitch.com/edit/#!/tiptap-sockets" target="_blank">socket server on glitch.com</a>. To keep the demo code clean, only a few nodes and marks are activated. There is also set a 250ms debounce for all changes. Try it out below:
    </div>
    <!-- <template v-if="editor && !loading"> -->
      <div class="count">
        {{ count }} {{ count === 1 ? 'user' : 'users' }} connected
      </div>
      <editor-content class="editor__content" :editor="editor" id="editor"  />
    <!-- </template>
    <em v-else>
      Connecting to socket server …
    </em> -->


    <!-- <div id="editor" :editor="editor">
        <p>Hello World!</p>
    </div> -->
  </div>
</template>

<script>
import io from 'socket.io-client'
import { Editor, EditorContent } from 'tiptap'

import Quill from 'quill'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.core.js'

import {
  HardBreak,
  Heading,
  Bold,
  Code,
  Italic,
  History,
  Collaboration,
} from 'tiptap-extensions'
export default {
  components: {
    EditorContent,
  },
  data() {
    return {
      loading: true,
      editor: null,
      socket: null,
      count: 0,

      value: '',
        toolbarOptions: [
            ['bold', 'italic', 'underline', 'strike'],      // 加粗，斜体，下划线，删除线
            ['blockquote', 'code-block'],                   // 引用，代码块

            [{'header': 1}, {'header': 2}],                 // 标题，键值对的形式；1、2表示字体大小
            [{'list': 'ordered'}, {'list': 'bullet'}],      // 列表
            [{'script': 'sub'}, {'script': 'super'}],       // 上下标
            [{'indent': '-1'}, {'indent': '+1'}],           // 缩进
            [{'direction': 'rtl'}],                         // 文本方向

            [{'size': ['small', false, 'large', 'huge']}],  // 字体大小
            [{'header': [1, 2, 3, 4, 5, 6, false]}],        // 几级标题

            [{'color': []}, {'background': []}],            // 字体颜色，字体背景颜色
            [{'font': []}],                                 // 字体
            [{'align': []}],                                // 对齐方式

            ['clean'],                                      // 清除字体样式
            ['image', 'video']                              // 上传图片、上传视频
        ],

        editor: new Editor({
            content: '<p>This is just a boring paragraph</p >',
        }),
    }
  },
  methods: {
    onInit({ doc, version }) {
      this.loading = false
      if (this.editor) {
        this.editor.destroy()
      }
      this.editor = new Editor({
        content: doc,
        extensions: [
          new HardBreak(),
          new Heading({ levels: [1, 2, 3] }),
          new Bold(),
          new Code(),
          new Italic(),
          new History(),
          new Collaboration({
            // the initial version we start with
            // version is an integer which is incremented with every change
            version,
            // debounce changes so we can save some requests
            debounce: 250,
            // onSendable is called whenever there are changed we have to send to our server
            onSendable: ({ sendable }) => {
              this.socket.emit('update', sendable)
            },
          }),
        ],
      })
    },
    setCount(count) {
      this.count = count
    },
  },
  mounted() {
    // server implementation: https://glitch.com/edit/#!/tiptap-sockets
    this.socket = io('ws://192.168.1.2:3000')
      // get the current document and its version
      .on('init', data => this.onInit(data))
      // send all updates to the collaboration extension
      .on('update', data => this.editor.extensions.options.collaboration.update(data))
      // get count of connected users
      .on('getCount', count => this.setCount(count))


        var editor = new Quill('#editor', {
            // modules: {toolbar: '#toolbar'},
            modules: {toolbar: this.toolbarOptions},
            theme: 'snow',
            
            placeholder: '请输入正文'
        });
  },
  beforeDestroy() {
    this.editor.destroy()
    this.socket.destroy()
  },
}
</script>

<style>
/* // @import "~variables"; */
.count {
  display: flex;
  align-items: center;
  font-weight: bold;
  /* color: rgba($color-black, 0.5); */
  color: #27b127;
  margin-bottom: 1rem;
  text-transform: uppercase;
  font-size: 0.7rem;
  line-height: 1;
}
  .count:before {
    content: '';
    display: inline-flex;
    background-color: #27b127;
    width: 0.4rem;
    height: 0.4rem;
    border-radius: 50%;
    margin-right: 0.3rem;
  }
</style>