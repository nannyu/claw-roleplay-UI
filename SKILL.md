---
name: star-office-ui
description: Star Office UI 使用手册：多 Agent 像素办公室部署、状态接入、移动端查看、公网发布与安全边界。
---

# Star Office UI Skill

本 Skill 面向两类用户：
1) 想快速跑起可视化办公室的人
2) 想让自己的 OpenClaw（龙虾）加入协作看板的人

---

## 1. 你能做什么

- 在办公室里可视化主 Agent 状态（工作 / 闲置 / 同步 / 报错）
- 邀请其他 Agent 加入并实时显示状态
- 在 UI 展示“昨日小记”微型总结
- 在手机端查看办公室动态
- 用 Cloudflare Tunnel 或你自己的公网地址对外访问

---

## 2. 状态规范（建议统一）

支持状态：
- `idle`
- `writing`
- `researching`
- `executing`
- `syncing`
- `error`

区域映射：
- `idle -> breakroom`
- `writing/researching/executing/syncing -> writing`
- `error -> error`

---

## 3. 本地快速启动

```bash
cd star-office-ui
python3 -m pip install -r backend/requirements.txt
cp state.sample.json state.json
cd backend && python3 app.py
```

打开：`http://127.0.0.1:18791`

切状态示例：
```bash
python3 set_state.py writing "工作中"
python3 set_state.py idle "待命"
```

---

## 4. 邀请访客（其他龙虾）加入

### 步骤 A：join
调用 `POST /join-agent`，提交：
- `name`
- `joinKey`
- `state`（可选，默认 idle）
- `detail`（可选）

成功后会返回 `agentId`。

### 步骤 B：push
定时调用 `POST /agent-push`，提交：
- `agentId`
- `joinKey`
- `state`
- `detail`（可选）

建议 10~20 秒推一次。

### 步骤 C：leave（可选）
调用 `POST /leave-agent` 离开房间。

---

## 5. 昨日小记说明

- 接口：`GET /yesterday-memo`
- 数据来源：工作区上级目录的 `memory/*.md`
- 行为：优先读取“昨天”；若没有则回退最近可用日期
- 会做基础隐私清理（ID / 路径 / 邮箱 / IP）

---

## 6. 公网访问建议

### 推荐：Cloudflare Tunnel
优点：快、便捷、无需改路由。

```bash
cloudflared tunnel --url http://127.0.0.1:18791
```

### 也可以使用你自己的公网方案
例如：
- Nginx/Caddy 反向代理
- 自有域名 + HTTPS
- 任何你熟悉的公网入口

> 注意：请勿在仓库中硬编码私有域名。

---

## 7. 常见问题

### Q1：访客一直在休息区
- 远端是否一直 push `idle`
- 是否读取错状态源文件
- `/agent-push` 是否成功返回

### Q2：join 报 403 / 429
- 403：join key 无效或不匹配
- 429：同 key 并发达到上限（默认 3）

### Q3：状态文件删了怎么办
- `state.json` / `agents-state.json` 是运行态文件
- 可由脚本运行时自动生成，或从 `state.sample.json` 复制
- 这类文件不建议提交到 GitHub

---

## 8. 开源与版权边界（必须遵守）

- 代码逻辑：MIT
- 美术资产：仅学习/演示用途，禁止商用
- 访客角色资产来源：LimeZu
  - https://limezu.itch.io/animated-mini-characters-2-platform-free
- 若要商用，请替换成你自己的美术资产

---

## 9. 给贡献者的建议

如果你想做二次开发，推荐优先从这几块下手：
- 更强的权限模型（管理员/访客分级）
- 多房间与团队组织
- 状态事件回放与日报自动生成
- 更标准的 API 文档与 SDK
