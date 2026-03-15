# 个人博客（Hugo + Hextra）

本项目基于 Hextra 官方文档中的「Git 子模块」方式搭建：

- 主题：`themes/hextra`（Git submodule）
- 配置：`hugo.yaml` 中 `theme: hextra`

## 前置要求

- Hugo（建议扩展版）
- Git

如果你本机已经安装 Hugo，直接使用 `hugo` 命令即可。  
如果还没安装，可临时使用当前目录里的 `./bin/hugo`。

## 本地启动

```bash
hugo server --buildDrafts --disableFastRender
```

启动后访问：`http://localhost:1313`

## 关闭预览

```bash
pkill -f "hugo.*server"
```

## 新建文章

```bash
hugo new content/blog/my-post.md
```

## 构建静态文件

```bash
hugo --gc --minify
```

构建产物位于 `public/` 目录。

## GitHub 账号

站点菜单中的 GitHub 链接当前指向：`https://github.com/liyongzheng666`

## 部署到 GitHub Pages（`*.github.io`）

仓库名请使用：`liyongzheng666.github.io`

本项目已包含工作流文件：
`.github/workflows/pages.yaml`

首次发布步骤：

```bash
git add .
git commit -m "init blog with hextra and pages workflow"
git remote add origin git@github.com:liyongzheng666/liyongzheng666.github.io.git
git push -u origin main
```

然后到 GitHub 仓库设置：
`Settings -> Pages -> Build and deployment -> Source = GitHub Actions`
