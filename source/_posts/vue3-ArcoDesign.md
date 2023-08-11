---
title: Vue3 使用ArcoDesign
index_img: /img/acrodesign/vue.png
categories:
- Vue3
tags:
- 前端
---
<!-- Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues). -->
## 关于 ArcoDesign

[ArcoDesign](https://arco.design/vue/docs/start) 是基于字节跳动公司内部的 Byte Design 升级而来，在打磨了近 3 年之后，通过字节内部大量业务沉淀和验证在前两天由掘金举办的《稀土开发者大会2021》上开源了，这不仅仅是一款 UI 组件库，而是一个能力全面的企业级产品设计系统。

## 开始使用

### 安装
``` js
npm install --save-dev @arco-design/web-vue
```

### 在main.js内引入
``` js
import { createApp } from 'vue'
import ArcoVue from '@arco-design/web-vue';
import App from './App.vue';
//完整引入css -不推荐
// import '@arco-design/web-vue/dist/arco.css';

const app = createApp(App);
app.use(ArcoVue); //将acro挂载到vue上
app.mount('#app')
```

### 按需加载（模板）
> 安装所需插件      npm install -D unplugin-vue-components unplugin-auto-import

安装完成，对 **vite.config.ts** 文件进行配置 

``` js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite';
import { ArcoResolver } from 'unplugin-vue-components/resolvers';

export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      resolvers: [ArcoResolver()],
    }),
    Components({
      resolvers: [
        ArcoResolver({
          sideEffect: true
        })
      ]
    })
  ]
});

```
### 按需加载（样式）
> 安装所需插件      npm install vite-plugin-style-import --save-dev

安装完成，对 **vite.config.ts** 文件进行配置 

``` js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'


// 自动导入vue中hook reactive ref等
import AutoImport from "unplugin-auto-import/vite"
//自动导入ui-组件 比如说ant-design-vue  element-plus等
import Components from 'unplugin-vue-components/vite'
import { ArcoResolver } from 'unplugin-vue-components/resolvers';
import { createStyleImportPlugin } from 'vite-plugin-style-import'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      //安装两行后你会发现在组件中不用再导入ref，reactive等
      imports: ['vue', 'vue-router'],
      //存放的位置
      dts: "src/auto-import.d.ts",
      resolvers: [ArcoResolver()],
    }),
    Components({
      // 引入组件的,包括自定义组件
      // 存放的位置
      dts: "src/components.d.ts",
      resolvers: [
        ArcoResolver({
          sideEffect: true
        })
      ]
    }),
    createStyleImportPlugin({
      libs: [
        {
          libraryName: '@arco-design/web-vue',
          esModule: true,
          resolveStyle: (name) => {
            // css
            return `@arco-design/web-vue/es/${name}/style/css.js`
            // less
            return `@arco-design/web-vue/es/${name}/style/index.js`
          },
        }
      ]
    })
  ],
})
```
> 若报 Cannot find module 'consola' 这个错误 
> 可在控制台输入  npm i consola -D 安装对应插件即可




完成以上步骤 就可以正常使用AcroDesign ui库了 


vue3+vite+ts+router+pinia+axios [一把梭推荐文章](https://blog.csdn.net/weixin_59916662/article/details/127331094?spm=1001.2014.3001.5506)

