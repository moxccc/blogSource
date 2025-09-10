# Mo博客源码

这是一个基于 [Hexo](https://hexo.io/) 静态站点生成器构建的个人博客源码仓库。

## 📖 项目简介

- **博客名称**: Mo博客
- **作者**: 阿呆
- **博客地址**: https://blog.moxccc.com/
- **技术栈**: Hexo + Fluid 主题
- **部署方式**: GitHub Pages

## 🚀 快速开始

### 环境要求

- Node.js (推荐使用 LTS 版本)
- npm 或 yarn

### 本地开发

1. **克隆仓库**
   ```bash
   git clone https://github.com/moxccc/blogSource.git
   cd blogSource
   ```

2. **安装依赖**
   ```bash
   npm install
   ```

3. **启动本地服务器**
   ```bash
   npm run server
   # 或者
   hexo server
   ```

   访问 http://localhost:4000 即可预览博客

### 构建和部署

1. **生成静态文件**
   ```bash
   npm run build
   # 或者
   hexo generate
   ```

2. **部署到 GitHub Pages**
   ```bash
   npm run deploy
   # 或者
   hexo deploy
   ```

## ✍️ 内容管理

### 创建新文章

```bash
hexo new post "文章标题"
```

这会在 `source/_posts/` 目录下创建一个新的 Markdown 文件。

### 创建新页面

```bash
hexo new page "页面名称"
```

### 文章格式

文章使用 Markdown 格式编写，支持以下 Front Matter:

```yaml
---
title: 文章标题
date: 2023-01-01 12:00:00
tags:
  - 标签1
  - 标签2
categories:
  - 分类名
---

文章内容...
```

## 🎨 主题配置

本博客使用 [Fluid](https://github.com/fluid-dev/hexo-theme-fluid) 主题，主要配置文件：

- `_config.yml` - Hexo 主配置文件
- `_config.fluid.yml` - Fluid 主题配置文件

### 主要配置项

- **站点信息**: 在 `_config.yml` 中的 Site 部分
- **主题样式**: 在 `_config.fluid.yml` 中自定义
- **部署设置**: 在 `_config.yml` 中的 deploy 部分

## 📁 目录结构

```
blogSource/
├── _config.yml          # Hexo 主配置文件
├── _config.fluid.yml    # Fluid 主题配置
├── package.json         # 项目依赖
├── scaffolds/           # 文章模板
├── source/             
│   ├── _posts/         # 博客文章
│   └── about/          # 关于页面
└── themes/             # 主题文件（如果有本地主题）
```

## 📝 可用命令

```bash
# 开发相关
npm run server          # 启动本地服务器
npm run build          # 生成静态文件
npm run clean          # 清理缓存和生成的文件
npm run deploy         # 部署到远程

# Hexo 命令
hexo new [layout] <title>    # 创建新文章/页面
hexo generate               # 生成静态文件
hexo server                # 启动本地服务器
hexo deploy                # 部署
hexo clean                 # 清理
```

## 🔧 常见问题

### 1. 依赖安装失败
如果 `npm install` 失败，尝试：
```bash
rm -rf node_modules package-lock.json
npm install --force
```

### 2. 生成失败
清理缓存后重新生成：
```bash
npm run clean
npm run build
```

### 3. 部署失败
检查 `_config.yml` 中的 deploy 配置是否正确，确保有正确的 Git 仓库访问权限。

## 🤝 贡献

欢迎提交 Issues 和 Pull Requests 来改进博客！

## 📄 许可证

请根据需要添加适当的许可证文件。

---

**更多 Hexo 使用教程**: https://hexo.io/docs/