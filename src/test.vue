<template>
    <div id="example">
        <div>{{text}}</div>
        <!-- <mavon-editor v-model="value"></mavon-editor> -->
<!-- 
        <div id="toolbar">
            <button class="ql-bold">Bold</button>
            <button class="ql-italic">Italic</button>
        </div> -->
 
        <!-- <div id="editor"> -->
            <!-- <p>Hello World!</p> -->
        <!-- </div> -->

        <editor-content :editor="editor" />

 
    </div>
</template>

<script>
// import mavonEditor from 'mavon-editor'
// import 'mavon-editor/dist/css/index.css'

import Quill from 'quill'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.core.js'

import io from 'socket.io-client'
import { Editor, EditorContent } from 'tiptap'

export default {
    components: {
        // 'mavon-editor': mavonEditor.mavonEditor
        EditorContent
    },
    data() {
        return {
            text: 'abc',
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
    mounted() {
        // var editor = new Quill('#editor', {
        //     // modules: {toolbar: '#toolbar'},
        //     modules: {toolbar: this.toolbarOptions},
        //     theme: 'snow',
            
        //     placeholder: '请输入正文'
        // });
    },
    beforeDestroy() {
        // Always destroy your editor instance when it's no longer needed
        this.editor.destroy()
    },
}

</script>

<style>
#example {
    color: #666;
}

</style>