# 部署：App Store Connect 状态变化 → Slack（Docker 常驻）

本仓库已配置为用 Docker 常驻轮询 App Store Connect，状态变化时推送到 Slack。

## 一次性准备

1. **App Store Connect API Key**
   - App Store Connect → Users and Access → Integrations → App Store Connect API
   - 生成一个 Key（角色 App Manager 或 Developer），下载 `AuthKey_XXXX.p8`
   - 记下 **Key ID** 和 **Issuer ID**
   - 把私钥放到本仓库：`mkdir -p secrets && cp ~/Downloads/AuthKey_XXXX.p8 secrets/AuthKey.p8`

2. **Slack Incoming Webhook**
   - 在 Slack 创建一个 App → 启用 Incoming Webhooks → 选目标频道 → 复制 Webhook URL

3. **填配置**
   ```sh
   cp .env.example .env
   # 编辑 .env，填入 Key ID / Issuer ID / Slack Webhook / bundle ids
   ```

## 运行

```sh
docker compose up -d        # 启动
docker compose logs -f      # 看日志
docker compose down         # 停止
```

## 说明

- 状态保存在命名卷 `asc-state`（`/app/data/kvstore.db`），重启不会重复通知。
- `.p8`、`.env`、状态目录都已在 `.gitignore` 中，不会被提交。
- 轮询间隔由 `.env` 的 `POLL_TIME_IN_SECONDS` 控制（默认 300 秒）。
- 默认 Slack 频道为 `#ios-app-updates`，可用 `SLACK_CHANNEL_NAME` 覆盖。
