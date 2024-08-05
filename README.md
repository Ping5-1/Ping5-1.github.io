# 说明

本项目基于[Astro](https://astro.build)搭建，搭建详情见[博客](https://github.com/saicaca/fuwari/generate)

## 运行

1. 安装依赖：`pnpm install`、 `pnpm add sharp`
   - 如未安装pnpm请先安装[pnpm](https://pnpm.io) `npm install -g pnpm`
2. `pnpm dev`
3. 注：node参考版本`18.17.0`

## 博客头部

```yaml
---
title: My First Blog Post
published: 2023-09-09
description: This is the first post of my new Astro blog.
image: /images/cover.jpg
tags: [Foo, Bar]
category: Front-end
draft: false
---
```

## 图标
[icones](https://icones.js.org/)

## 脚本指令

下列指令均需要在项目根目录执行：

| Command                            | Action                                 |
| :--------------------------------- | :------------------------------------- |
| `pnpm install` 并 `pnpm add sharp` | 安装依赖                               |
| `pnpm dev`                         | 在 `localhost:4321` 启动本地开发服务器 |
| `pnpm build`                       | 构建网站至 `./dist/`                   |
| `pnpm preview`                     | 本地预览已构建的网站                   |
| `pnpm new-post <filename>`         | 创建新文章                             |
| `pnpm astro ...`                   | 执行 `astro add`, `astro check` 等指令 |
| `pnpm astro --help`                | 显示 Astro CLI 帮助                    |
