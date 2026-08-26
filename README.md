# test-111 测试站

测试站：**https://xinjiu-qwq.github.io/test-111/**
线上后台（Decap CMS）：**https://xinjiu-qwq.github.io/test-111/admin/**

本目录支持 **三种管理方式**，最终都通过 GitHub Actions 自动构建部署。

## 一、Decap 网页后台（线上，任意设备可用）

1. 打开 https://xinjiu-qwq.github.io/test-111/admin/
2. 点 **Login with GitHub**（走 Cloudflare Worker 认证）
3. 在线新建 / 编辑 / 删除文章，保存 = 自动提交到 `source` 分支
4. GitHub Actions 自动构建 → 1-2 分钟后网站更新

## 二、hexo-admin 本地后台（可视化，需本地运行）

hexo-admin 是 hexo 的本地管理插件（已安装并打过 hexo 8 兼容补丁），提供图形化写作界面：

```powershell
cd C:\Users\ADMIN\Desktop\1\test-111
npx hexo server        # 启动本地服务
```

然后浏览器打开 **http://localhost:4000/test-111/admin/**（注意：地址带 `/test-111/` 前缀，这是项目站点的 root 路径）。

功能：可视化新建/编辑文章、上传图片、管理页面、一键部署。
⚠️ **不要点界面里的 Deploy 按钮**——它会直接推 public 到 main，绕过 CI 且与 CI 部署冲突；发布请用 `git push`（见下）。

## 三、本地 Hexo 命令行

```powershell
cd C:\Users\ADMIN\Desktop\1\test-111

# 先同步远程（重要：网页后台可能改过代码）
git pull

# 新建文章（生成 markdown 后手动编辑内容）
npx hexo new "文章标题"

# 编辑 source/_posts/xxx.md ...
# 发布：提交源码，由 CI 自动构建部署
git add -A
git commit -m "更新文章"
git push
```

## ⚠️ 三种方式共存的关键规则

| 规则 | 原因 |
|---|---|
| **不要用 `npx hexo deploy` / hexo-admin 的 Deploy 按钮** | 会把 public/ 直接推 main，绕过 CI，与 CI 部署互相覆盖 |
| **本地操作前先 `git pull`** | 网页后台的提交也在 source 分支，不拉取会推送冲突 |
| **发布统一走 `git push`** | CI 是唯一的部署者 |

## 架构

- `source` 分支：Hexo 源码（各种方式共同编辑的对象）
- `main` 分支：构建后的静态网站（GitHub Pages 服务）
- `.github/workflows/deploy.yml`：自动构建部署工作流
- `patches/`：hexo-admin 的 hexo 8 兼容补丁（pnpm patch，安装时自动应用）
- 认证代理：Cloudflare Worker（源码在 `C:\Users\ADMIN\Desktop\1\decap-proxy`）

## 相关文档

- `CF_DEPLOY.md`：Cloudflare Worker 部署说明
- `ADMIN_SETUP.md`：Decap 后台使用说明
