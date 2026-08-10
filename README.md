
# 📢 RSS Monitor

> 一个轻量级、模块化的 RSS 订阅监控工具，通过 Discord Webhook 发送带有精美 Embed 样式和图片预览的通知。

当前版本：**V1.0.10**
版本更新时间：2026-08-10

---

## ✨ 核心特性

*   **智能图片预览**：自动提取 RSS 文章中的图片（附件、媒体内容或正文图片），在 Discord 推送中大图展示。
*   **抗封锁机制**：
    *   **429 重试**：遇到反爬虫限制（Too Many Requests）时自动退避重试。
    *   **循环模式**：GitHub Actions 每次运行 45 分钟，通过 `--time-limit` 参数优雅退出并保存数据库，确保持续监控。
*   **Discord 深度集成**：
    *   使用 Embeds 样式，美观大方。
    *   支持自定义颜色（随机生成）、Footer 时间戳（北京时间）。
*   **自动化部署**：
    *   支持 **GitHub Actions** 免费运行。
    *   支持 **Docker/Zeabur** 容器化部署。
*   **数据持久化**：使用 SQLite 记录已推送条目，防止重复打扰。

---

## 🚀 快速开始

### 1. 准备配置

创建或修改 `rss.yaml`，填入你想监控的 RSS 源：

```yaml
"技术社区":
  "rss_url": "https://rsshub.app/t66y/7"
  "website_name": "T66y技术"

"Pornhub - 国产":
  "rss_url": "https://rssmonitor.zeabur.app/pornhub/search/%E5%9B%BD%E4%BA%A7"
  "website_name": "Pornhub - 国产"
```

### 2. 本地运行

安装依赖并启动：

```bash
git clone https://github.com/TreasureBoy99/NSFW_monitor.git && cd NSFW_monitor
pip install -r requirements.txt
python Rss_monitor.py
```

---

## 🛠️ 部署指南

### 方式一：GitHub Actions (免费)

本项目内置了自动化工作流，Fork 后即可使用。

1.  **Fork** 本仓库。
2.  进入仓库 **Settings** -> **Secrets** -> **Actions**。
3.  配置环境变量：
    *   `DISCARD_WEBHOOK`: 你的 Discord Webhook 地址。
4.  (可选) 修改 `.github/workflows/rss_monitor.yml` 调整定时频率（默认每 2 小时运行一次，每次持续监控 45 分钟）。

### 方式二：Docker / Zeabur (推荐)

代码推送到 `main` 分支会自动构建镜像到 GHCR。

1.  **部署**：在 Zeabur 或 Docker 环境中使用镜像 `ghcr.io/你的用户名/nsfw_monitor:latest`。
2.  **环境变量**：
    *   `DISCARD_WEBHOOK`: Discord Webhook 地址。
    *   `DISCARD_SWITCH`: `ON`。
    *   `TZ`: `Asia/Shanghai` (默认已设)。
3.  **数据持久化配置 (Zeabur 示例)**：
    *   在 Zeabur 服务页面，点击 **Data Storage** (或 Volumes)。
    *   点击 **Add Volume**。
    *   **Mount Path (挂载路径)**: 填写 `/app/articles.db` (注意：这里直接挂载文件路径)。
    *   重启服务即可。

    > **注意**：如果不配置挂载卷，Docker 容器重启后 `articles.db` 会重置，通过 RSS 推送过的旧文章可能会被再次推送。

```bash
# Docker 手动运行示例
docker run -d \
  -e DISCARD_WEBHOOK="https://discord.com/api/webhooks/..." \
  -e DISCARD_SWITCH="ON" \
  -v $(pwd)/articles.db:/app/articles.db \
  ghcr.io/adminlove520/nsfw_monitor:latest
```

---

## 📜 项目结构

```
.
├── Rss_monitor.py          # 主程序入口
├── rss.yaml                # RSS 源配置文件
├── config.yaml             # 推送服务的本地配置文件 (可选)
├── articles.db             # SQLite 数据库 (自动生成)
├── utils/
│   ├── notify.py           # 推送逻辑 (Discord Embed)
│   ├── rss.py              # RSS 解析与重试逻辑
│   ├── config.py           # 配置加载
│   └── db.py               # 数据库初始化
└── .github/workflows/
    ├── rss_monitor.yml     # 监控任务工作流
    ├── deploy-image.yml    # Docker 镜像构建工作流
    └── version-bump.yml    # 自动版本管理工作流
```

## 📝 许可证

MIT License
