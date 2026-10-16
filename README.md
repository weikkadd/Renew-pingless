# Renew-pingless — Pingless 面板自动续期

[dash.pingless.org](https://dash.pingless.org) 免费服务器自动续期脚本（Python + SeleniumBase，GitHub Actions 定时运行）。

## 续期规则

- 服务器**每 48 小时**需续期一次，否则自动关机
- **24 小时标记后**按钮弹出，点击 "Renew Now" 即可（每次消耗 500 Credits）
- 脚本自动判断：未到续期时间（倒计时 > 24h）时跳过，不误点

## 部署步骤

1. Fork 或推送本仓库到你的 GitHub（**建议私密仓库**）
2. 仓库 Settings → Secrets and variables → Actions → New repository secret，逐个添加以下 secrets
3. 手动触发一次验证：Actions → Auto Renew Pingless → Run workflow

## 需要配置的 Secrets

| Secret | 必填 | 说明 |
|---|---|---|
| `DISCORD_TOKEN` | ✅ 必填 | Discord 用户 Token，备用登录方式。支持两种格式：纯 token，或 `备注,token`（取逗号后最后一段） |
| `EMAIL` | 可选 | 邮箱，仅用于 TG 通知里展示，可随意填 |
| `TG_BOT_TOKEN` | 可选 | Telegram Bot Token，配合 TG_CHAT_ID 一起填才生效 |
| `TG_CHAT_ID` | 可选 | Telegram chat id |
| `NODE_LINK` | 可选 | sing-box 代理节点链接。Actions 服务器一般可直连 Pingless，被墙时才需要 |

### DISCORD_TOKEN 获取方式

1. 浏览器登录 [discord.com](https://discord.com)（网页版）
2. F12 打开开发者工具 → Network（网络）标签
3. 刷新页面，随便点一个请求 → Request Headers（请求标头）
4. 找到 `Authorization:` 后面的那串（形如 `MTAxxx.yyy.zzz`），复制
5. ⚠️ Token 等同账号密码，切勿泄露；Discord 风控严格时可能需要重新获取

### TG 通知获取方式

1. Telegram 搜索 `@BotFather` → `/newbot` 创建机器人，得到 `TG_BOT_TOKEN`
2. 搜索 `@userinfobot` 或给你的 bot 发条消息后访问 `https://api.telegram.org/bot<TOKEN>/getUpdates`，取 `chat.id` 填 `TG_CHAT_ID`

## 工作流说明

- 定时：每天 UTC 2 点（北京时间 10 点）自动运行（GitHub 免费额度一般会推迟几分钟到几十分钟）
- 手动：Actions → Auto Renew Pingless → Run workflow 可随时手动触发
- 每次运行自动清理旧的工作流记录，只保留最近一次
- 运行日志和截图存放在 artifacts 里，失败时下载排查

## 常见问题

- **提示未到续期时间**：正常，倒计时 > 24h 时脚本只查看不点击，等剩余时间 ≤ 24h 再跑
- **代理相关**：脚本内 `PROXY_SERVER` 默认 `socks5://127.0.0.1:1080`（配合工作流的 sing-box）；本机直跑时设置环境变量 `IS_PROXY=true` + `PROXY_SERVER` 指向你的代理
- **Discord Token 失效（401）**：重新抓取 Token 更新 `DISCORD_TOKEN` secret