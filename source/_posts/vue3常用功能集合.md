---
title: vue3常用功能集合
date: 2024-03-28 09:07:42
categories:
- Vue3
tags:
- 前端
---

## 前言
{% note success %}
我们在敲代码过程中会遇到一些常见的功能，列如：导出pdf,因为比较常用，代码又繁琐，所以发篇文章记录一下，省的每次找麻烦。
{% endnote %}

### 导出PDF
1. 引入组件[jspdf](https://www.npmjs.com/package/jspdf)、[html2canvas](https://www.npmjs.com/package/html2canvas/v/1.4.1)，jspdf-用于生成各种用途的PDF，html2canvas-可以将HTML 元素转换为Canvas。
```
npm i html2canvas
npm i jspdf
```
2. 在`utils`文件夹下新建`html2pdf.js`文件
```
import html2canvas from 'html2canvas';
import jsPDF from 'jspdf'

export const htmlToPDF = async (htmlId, title= "报表", bgColor = "#fff", padding = 10) => {
    let pdfDom= document.getElementById(htmlId)
    pdfDom.style.padding = '0 10px !important'
    const A4Width = 595.28;
    const A4Height = 841.89;
    let canvas = await html2canvas(pdfDom, {
        scale: 2,
        useCORS: true,
        backgroundColor: bgColor,
    });
    //canvas.width / A4Width 计算出缩放比例 即画布宽度相对于 A4 纸宽度的比例
    let pageHeight = (canvas.width / A4Width) * A4Height;
    let leftHeight = canvas.height;
    let position = 0;
    let imgWidth = A4Width - padding * 2;
    let imgHeight = (A4Width / canvas.width) * canvas.height;
    /*
     根据自身业务需求  是否在此处键入下方水印代码
    */
    let pageData = canvas.toDataURL("image/jpeg", 1.0);
    let PDF = new jsPDF("p", 'pt', 'a4');
    if (leftHeight < pageHeight) {
        // 控制内边距
        PDF.addImage(pageData, "JPEG", 10, 20, imgWidth, imgHeight);
    } else {
        let pageImgIndex=0;
        while (leftHeight > 0) {
            canvas = await html2canvas(pdfDom, {
                scale: 2,
                useCORS: true,
                backgroundColor: bgColor,
            });
            let pageData = canvas.toDataURL("image/jpeg", 1.0);
            // -A4Height*pageImgIndex 每页的起始位置 20距顶部
            PDF.addImage(pageData, "JPEG", 10, -A4Height*pageImgIndex+20, imgWidth, imgHeight);
            leftHeight -= pageHeight;
            position -= A4Height;
            if (leftHeight > 0) PDF.addPage();
            pageImgIndex+=1;
        }
    }
    PDF.save(title + ".pdf");
}
```
3. 若需要水印，则在上方注释中粘贴下方代码
```
const ctx: any = canvas.getContext('2d');
ctx.textAlign = 'center';
ctx.textBaseline = 'middle';
ctx.rotate((20 * Math.PI) / 180);
ctx.font = '20px Microsoft Yahei';
ctx.fillStyle = 'rgba(184, 184, 184, 0.8)';
for (let i = canvas.width * -1; i < canvas.width; i += 240) {
    for (let j = canvas.height * -1; j < canvas.height; j += 200) {
        // 填充文字，x 间距, y 间距
        ctx.fillText('水印名', i, j);
    }
}
```
4. 在页面中使用
```
// 1.引入
import {htmlToPDF}  form '@/utils/html2pdf.js'

// 2.使用
htmlToPDF(`需要绘制元素ID`,`文件名称`)
```

### 使用富文本编辑器
1. 我使用的是[wangEditor](https://www.wangeditor.com/v5/)，首先先安装插件
```
npm i @wangeditor/editor-for-vue@next
```
2. 富文本编辑器一般都是以组件使用的，在`components`文件夹下新建`Editor/index.vue`文件，写入以下代码
```
<template>
    <div class="editor">
        <Toolbar style="border-bottom: 1px solid #ccc" :editor="editorRef" :defaultConfig="toolbarConfig" />
        <Editor style="height: 350px; overflow-y: hidden" v-model="form.content" :defaultConfig="editorConfig"
            @onCreated="handleCreated" />
    </div>
</template>

<script setup>
import { reactive, shallowRef, onBeforeUnmount, watch } from 'vue'
import axios from "axios";

// 富文本编辑器
import { Editor, Toolbar } from '@wangeditor/editor-for-vue'
import '@wangeditor/editor/dist/css/style.css' // 引入 css

//这里是父组件传过来的内容
let props = defineProps({
    EditorContent: {
        type: String,
        default: ''
    }
})
// 富文本内容
const form = reactive({
    content: ''
})
// 编辑器实例，必须用 shallowRef
const editorRef = shallowRef()
const toolbarConfig = {
    
}

// 记录 editor 实例，重要！
const handleCreated = (editor) => {
    editorRef.value = editor
    // 传入内容
    editorRef.value.setHtml(props.EditorContent);
}

// 自定义富文本上传图片，若不需要可忽略，
const uploadImg = (file, insertFn) => {
    let imgData = new FormData();
    imgData.append("file", file);
    //这里是调用接口，imgData这个是参数
    axios.post(`api`, imgData, {
        headers: {
            "Authorization": "Bearer " + token,
            'Content-Type': 'multipart/form-data'
        }
    }).then(res => {
        //把图片赋值在输入框内
        insertFn(res.data.data);
    }).catch((error) => {
        Notification.warning({
            title: "提示",
            content: "上传失败,请重新上传"
        });
    });
}

// 富文本默认配置
const editorConfig = {
    placeholder: '请输入内容...',
    MENU_CONF: {
        uploadImage: {
            // 自定义上传图片 方法
            customUpload: uploadImg,
            // server: `api`
        },
    }
}

// 组件销毁时，也及时销毁编辑器
onBeforeUnmount(() => {
    const editor = editorRef.value
    if (editor == null) return
    editor.destroy()
})


//父组件调用此方法，用于获取富文本框编辑器的内容
const getHtml = () => {
    return editorRef.value.getHtml()
}

defineExpose({ getHtml })
</script>

<style lang="scss" scoped>
.editor {
    border: 1px solid var(--color-border-3);

    &.w-e-full-screen-container {
        z-index: 9999;
    }

    :deep(.w-e-modal) {
        position: fixed;
        top: 50% !important;
        left: 50% !important;
        transform: translate(-50%, -50%);
    }
}
</style>
```
3. 扩展 一 `html代码编辑`，组件默认是没有html代码预览和编写功能的，需要自己自定义此功能，在`components`文件夹下新建`Editor/plugins/preHeml.js`文件，写入以下代码
```
import { Boot } from '@wangeditor/editor'
// 定义菜单 class
class MyModalMenu {
  title = "code";
  //图标
  iconSvg = `<svg t="1658298161465" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="2392" width="200" height="200"><path d="M390.272 919.552a53.077333 53.077333 0 0 1-38.186667-16.128L35.84 578.602667a96.298667 96.298667 0 0 1 0-133.333334l316.245333-324.821333a53.333333 53.333333 0 0 1 76.416 74.410667l-294.4 302.208a21.333333 21.333333 0 0 0 0 29.866666l294.4 302.165334a53.333333 53.333333 0 0 1-38.4 90.538666zM633.770667 919.552a53.333333 53.333333 0 0 1-38.4-90.538667l294.4-302.208a21.333333 21.333333 0 0 0 0-29.866666l-294.4-302.165334a53.333333 53.333333 0 0 1 76.416-74.410666l316.202666 324.778666a96.298667 96.298667 0 0 1 0 133.333334l-316.245333 324.864a53.077333 53.077333 0 0 1-37.973333 16.213333z m277.930666-399.914667z" p-id="2393"></path></svg>`;
  tag = "button";
  showModal = true;
  modalWidth = window.innerWidth * 0.6;
  $content = null;
  // eslint-disable-next-line no-unused-vars
  getValue(editor) {
    // 用不到 getValue
    return "";
  }
  isActive() {
    return false;
  }
  isDisabled() {
    return false;
  }
  // eslint-disable-next-line no-unused-vars
  async exec(editor, value) { }
  // eslint-disable-next-line no-unused-vars
  getModalPositionNode(editor) {
    return null;
  }
  // eslint-disable-next-line no-unused-vars
  getModalContentElem(editor) {
    const textarea = document.createElement("textarea");
    const h2 = document.createElement("h2");
    const $content = document.createElement("div");
    const updatabutton = document.createElement("button");
    const cancelbutton = document.createElement("button");
    const div = document.createElement("div");
    
    setstyle($content, {
      maxHeight: "600px",
      overflowY: "auto",
      position: "relative",
      overflowX: "hidden",
      width: "100%",
    });
    // 文本框
    setstyle(textarea, {
      minHeight: "450px",
      width: `90%`,
      padding: "0",
      marginLeft: "50%",
      transform: "translateX(-50%)",
    });
    setstyle(h2, {
      textAlign: "concent",
    });
    //修改按钮
    setstyle(div, {
      display: "flex",
      justifyContent: "flex-end",
      margin: "30px 0",
    });
    const button = {
      right: "40px",
    };

    setstyle(cancelbutton, { ...button, ...{} });
    setstyle(updatabutton, {
      ...button,
      ...{ backgroundColor: "#F0F8FF", marginLeft: "30px" },
    });

    h2.innerText = "code";
    updatabutton.innerText = "修改";
    cancelbutton.innerText = "取消";

    textarea.value = editor.getHtml();
    div.appendChild(cancelbutton);
    div.appendChild(updatabutton);
    $content.appendChild(h2);
    $content.appendChild(textarea);
    $content.appendChild(div);

    const updata = updatabutton.addEventListener('click', () => {
      let text = textarea.value.replace(/'/g, "'")
      if (text === '') text = ' '
      // const area = document.getElementById('w-e-textarea-1')
      // area.innerHTML = text
      console.log(text)
      editor.setHtml(text)
      editor.hidePanelOrModal()
      updatabutton.removeEventListener('click', updata)
    })
    const cancel = cancelbutton.addEventListener('click', () => {
      editor.hidePanelOrModal()
      cancelbutton.removeEventListener('click', cancel)
    })
    this.$content = $content
    return $content

  }
}

// 定义菜单配置
export const menuConf = {
  key: 'preHtmlCode', // menu key ，唯一。注册之后，可配置到工具栏
  factory() {
    return new MyModalMenu()
  },
}
export function setstyle(dom, styles) {
  Object.keys(styles).forEach((style) => {
      dom.style[style] = styles[style];
  });
}

Boot.registerMenu(menuConf)
```
4. 在`Editor/index.vue`使用
```
//引入
import { menuConf } from "./plugins/preHeml.js";

//注册
const toolbarConfig = {
    insertKeys: {
        index: 0,
        keys: ['preHtmlCode', "|"]
    }
}
```
5. 页面中使用
```
//使用组件 如果你的富文本框组件是放在弹框里使用的需要用v-if去判断组件的显示/隐藏
<Editor ref="EditorRef"  v-if="visible" v-model:EditorContent="form.content"></Editor>

// 定义富文本框编辑器元素
const EditorRef=ref();

//在提交的时候调用组件方法获取编辑器内容
form.content=EditorRef.value.getHtml();
```

