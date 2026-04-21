# MyFlatnotes 修改版

基于 [dullage/flatnotes](https://github.com/dullage/flatnotes) v5.5.4 的修改版本。

---

## 🔧 与原版的主要区别

| 功能 | 原版 flatnotes | 本修改版 |
|------|----------------|----------|
| **URL 导入 Markdown** | ❌ 无 | ✅ 新增 `/api/url-to-markdown` 接口，支持从网页 URL 导入内容并转换为 Markdown |
| **路由认证守卫** | ❌ 无 | ✅ 新增 `/api/auth-check` 接口，前端路由添加认证检查，防止未登录访问 |
| **错误处理优化** | 基础错误处理 | ✅ 400 错误显示详细 Toast 通知 |
| **Pinia 版本** | 2.3.1 | ✅ 升级至 3.0.3 |
| **Token 加载时机** | App.vue 早期加载 | ✅ 移至 index.js，配合路由守卫确保认证状态正确同步 |

---

## ✨ 新增功能详解

### 1. URL 导入 Markdown

在笔记编辑模式下，点击工具栏的 **"Import URL"** 按钮，可以将任意网页的内容转换为 Markdown 并插入到当前笔记中。

**技术实现：**
- 后端：`POST /api/url-to-markdown` 接口，使用 `html2text` 库将 HTML 转为 Markdown
- 前端：Note.vue 新增 URL 导入弹窗，调用 `urlToMarkdown()` API
- 支持超时处理（15秒）、重定向跟随、错误提示

### 2. 路由认证守卫

修改了前端路由系统，在用户首次导航时检查认证状态：

```
beforeEach 守卫 → 调用 /api/auth-check → 
  - 成功：允许访问
  - 401：重定向到登录页（保留原目标路径）
```

### 3. CONTRIBUTING.md 更新

原项目的 CONTRIBUTING.md 已更新为更明确的社区沟通指南，说明当前暂停接受 Pull Request 的原因和维护策略。

---

## 📦 版本信息

- **基础版本：** flatnotes v5.5.4
- **本版本：** v5.5.4-mod
- **前端依赖更新：** pinia 2.3.1 → 3.0.3

---

## 🚀 快速开始

### Docker 部署

```yaml
version: "3"

services:
  flatnotes:
    container_name: flatnotes
    image: dullage/flatnotes:latest
    environment:
      PUID: 1000
      PGID: 1000
      FLATNOTES_AUTH_TYPE: "password"
      FLATNOTES_USERNAME: "user"
      FLATNOTES_PASSWORD: "changeMe!"
      FLATNOTES_SECRET_KEY: "aLongRandomSeriesOfCharacters"
    volumes:
      - "./data:/data"
    ports:
      - "8080:8080"
    restart: unless-stopped
```

### 自行构建

```bash
# 构建前端
cd client && npm install && npm run build && cd ..

# 使用 Docker 构建
docker build -t myflatnotes .
```

---

## 📁 项目结构

```
flatnotes-src/
├── client/              # Vue.js 前端
│   ├── views/Note.vue   # 笔记页面（含 URL 导入功能）
│   ├── api.js           # API 调用（含 urlToMarkdown）
│   ├── router.js        # 路由守卫
│   └── components/      # UI 组件
├── server/              # Python FastAPI 后端
│   ├── main.py          # 主服务（含 url-to-markdown 接口）
│   └── ...
├── Dockerfile.custom    # 自定义 Dockerfile
└── entrypoint.sh        # 启动脚本
```

---

## 🔑 核心配置

新增的后端 API 无需额外环境变量，直接可用：

| 接口 | 方法 | 参数 | 说明 |
|------|------|------|------|
| `/api/auth-check` | GET | - | 认证检查，返回 "OK" |
| `/api/url-to-markdown` | POST | `url` (query) | 将 URL 内容转为 Markdown |

---

## ⚠️ 注意事项

1. **URL 导入依赖** `html2text` 和 `httpx` 库，已包含在依赖中
2. **认证方式** 与原版一致，支持 none/read-only/password/2FA
3. **数据存储** 纯 Markdown 文件，无数据库依赖

---

## 📄 License

继承原 flatnotes MIT License。
