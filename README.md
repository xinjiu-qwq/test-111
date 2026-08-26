# test-111 测试站

测试站：**https://xinjiu-qwq.github.io/test-111/**
后台管理：**https://xinjiu-qwq.github.io/test-111/admin/**（Decap CMS）

本目录同时支持 **本地 Hexo 管理** 和 **Decap 网页管理** 两种方式，两者都通过 GitHub Actions 自动构建部署。

## 一、Decap 网页管理（推荐日常使用）

1. 打开 https://xinjiu-qwq.github.io/test-111/admin/
2. 点 **Login with GitHub**（走 Cloudflare Worker 认证）
3. 在线新建 / 编辑 / 删除文章，保存 = 自动提交到 `source` 分支
4. GitHub Actions 自动构建 → 1-2 分钟后网站更新

## 二、本地 Hexo 管理（命令行）

```powershell
cd C:\Users\ADMIN\Desktop\1\test-111

# 先同步远程（重要：网页后台可能改过代码）
git pull

# 新建文章（生成 markdown 后手动编辑内容）
npx hexo new "文章标题"

# 编辑 source/_posts/xxx.md ...
# 本地预览
npx hexo server        # http://localhost:4000

# 发布：提交源码，由 CI 自动构建部署
git add -A
git commit -m "更新文章"
git push
```

## ⚠️ 两种方式共存的关键规则

| 规则 | 原因 |
|---|---|
| **不要在本目录执行 `npx hexo deploy`** | hexo deploy 会把 public/ 直接推送到 main 分支，绕过 CI，与 CI 的部署互相覆盖 |
| **本地操作前先 `git pull`** | 网页后台的提交也在 source 分支上，不拉取会推送冲突 |
| **发布统一走 `git push`** | CI 是唯一的部署者，两种方式改完都由它构建 |

## 架构

- `source` 分支：Hexo 源码（本地和网页后台共同编辑的对象）
- `main` 分支：构建后的静态网站（GitHub Pages 服务）
- `.github/workflows/deploy.yml`：自动构建部署工作流
- 认证代理：Cloudflare Worker（源码在 `C:\Users\ADMIN\Desktop\1\decap-proxy`）

## 相关文档

- `CF_DEPLOY.md`：Cloudflare Worker 部署说明
- `ADMIN_SETUP.md`：后台使用说明
