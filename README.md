# Lumos 博客

基于 [Hexo](https://hexo.io) + [Fluid](https://github.com/fluid-dev/hexo-theme-fluid) 主题搭建的个人博客。

## 快速开始

### 环境要求

- Node.js 18+
- pnpm（推荐）或 npm
- Git

### 本地预览

```bash
# 安装依赖
pnpm install

# 启动本地服务器
pnpm server
# 或
hexo server
```

访问 http://localhost:4000 预览博客。

### 创建新文章

```bash
hexo new "文章标题"
```

文章会创建在 `source/_posts/文章标题.md`。

### 生成静态文件

```bash
hexo generate
# 或
pnpm build
```

### 部署到 GitHub Pages

```bash
hexo deploy
# 或
pnpm deploy
```

## 项目结构

```
boke/
├── _config.yml           # 站点配置文件
├── _config.fluid.yml     # Fluid 主题配置文件
├── package.json          # 项目依赖
├── scaffolds/            # 文章模板
├── source/               # 源文件
│   ├── _posts/           # 博客文章
│   ├── about/            # 关于页面
│   ├── categories/       # 分类页面
│   └── tags/             # 标签页面
├── themes/               # 主题目录
└── public/               # 生成的静态文件（hexo generate）
```

## 配置说明

### 站点配置 (_config.yml)

```yaml
# 站点信息
title: Lumos                    # 博客名称
subtitle: '探索 · 记录 · 分享'   # 副标题
author: Lu                      # 作者
language: zh-CN                 # 语言
url: https://lukangyu.github.io # 博客地址

# 主题
theme: fluid

# 部署配置
deploy:
  type: git
  repo: https://github.com/lukangyu/lukangyu.github.io.git
  branch: main
```

### 主题配置 (_config.fluid.yml)

主要配置项：

- **导航菜单**：`navbar.menu`
- **首页 Banner**：`index.banner`
- **社交链接**：`social.links`
- **代码高亮**：`code`
- **数学公式**：`post.math`
- **站内搜索**：`search`

详细配置请参考 [Fluid 官方文档](https://hexo.fluid-dev.com/docs/)。

## 写作指南

### 文章格式

```markdown
---
title: 文章标题
date: 2026-02-14 12:00:00
categories: 
  - 分类名称
tags:
  - 标签1
  - 标签2
---

这里是文章正文...
```

### 插入图片

1. 将图片放在 `source/images/` 目录
2. 使用相对路径引用：`![](/images/图片名.png)`

或启用文章资源文件夹（`post_asset_folder: true`）：

```bash
hexo new "文章标题"
# 会同时创建 source/_posts/文章标题/ 文件夹
```

### 数学公式

已启用 KaTeX 支持：

```markdown
行内公式：$E = mc^2$

块级公式：
$$
\sum_{i=1}^{n} i = \frac{n(n+1)}{2}
$$
```

### 代码块

支持代码高亮：

````markdown
```python
def hello():
    print("Hello, World!")
```
````

## 常用命令

| 命令 | 说明 |
|------|------|
| `hexo new "标题"` | 新建文章 |
| `hexo new page "页面名"` | 新建页面 |
| `hexo generate` | 生成静态文件 |
| `hexo server` | 启动本地服务器 |
| `hexo deploy` | 部署到远程仓库 |
| `hexo clean` | 清除缓存和静态文件 |

## 网络代理配置

如果在中国大陆部署时遇到网络问题，需配置 Git 代理：

```bash
# 设置代理（根据你的代理端口修改）
git config --global http.proxy http://127.0.0.1:7897
git config --global https.proxy http://127.0.0.1:7897

# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## 更新主题

```bash
pnpm update hexo-theme-fluid
```

更新后检查 [_config.fluid.yml](https://github.com/fluid-dev/hexo-theme-fluid/blob/master/_config.yml) 是否有新配置项。

## 思源笔记发布配置

通过思源笔记的发布工具插件，可以直接将笔记发布到本博客。

### 安装发布工具

在思源笔记集市中安装「发布工具」插件。

### 配置步骤

#### 1. 启用 Hexo 平台

1. 点击发布工具图标
2. 依次选择 **通用设置 → 发布设置**
3. 点击 **插件商店 → Github → Hexo**
4. 点击 **启用**，然后 **提交**

#### 2. 配置 Hexo 平台

点击 **通用设置 → 发布设置**，找到新增的 Hexo 平台，点击 **设置**：

**必需配置项：**

| 配置项 | 值 |
|--------|-----|
| GitHub Token | 你的 GitHub Personal Access Token |
| GitHub 仓库 | lukangyu/lukangyu.github.io |
| 分支 | main |
| 发布目录 | source/_posts |

**文件规则配置：**

```
[yyyy]/[MM]/[filename].md
```

可用占位符：
- `[yyyy]` 年份，如 2026
- `[MM]` 月份，如 02
- `[dd]` 日期，如 14
- `[filename]` 文件名
- `[slug]` 别名

#### 3. 图片存储配置

在 **图床服务与图片存储路径** 中选择 **当前平台**：

| 配置项 | 值 |
|--------|-----|
| 图片存储路径 | source/images |
| 图片链接地址 | /images |

#### 4. YAML 预设

可配置文章的默认 Front-matter：

```yaml
categories:
  - 默认分类
tags:
  - 标签
```

### 发布文章

1. **一键发布**：点击 **一键发布 → hexo** 即可快速发布
2. **常规发布**：点击 **常规发布 → Hexo** 可进行个性化设置后发布

### 删除文章

在常规发布界面：
- **取消**：删除远程文章并解除关联
- **强制删除**：仅解除关联（用于远程文章已删除的情况）

### 参考链接

- [思源笔记发布工具文档](https://siyuan.wiki/x/20230908182140-8riar0r)
- [发布工具测试博客](https://hexo.terwer.space)
- [测试博客源码](https://github.com/terwer/hexo-blog)

---

## 相关链接

- [Hexo 文档](https://hexo.io/docs/)
- [Fluid 主题文档](https://hexo.fluid-dev.com/docs/)
- [Fluid 主题配置指南](https://hexo.fluid-dev.com/docs/guide/)
- [GitHub Pages](https://pages.github.com/)

## License

MIT
