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
        while (leftHeight > 0) {
            PDF.addImage(pageData, "JPEG", 10, 20, imgWidth, imgHeight);
            leftHeight -= pageHeight;
            position -= A4Height;
            if (leftHeight > 0) PDF.addPage();
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
