# Star Office UI

一个面向多 Agent 协作的像素办公室看板：把 AI 助手（OpenClaw / 龙虾）的工作状态实时可视化，帮助团队直观看到“谁在做什么、昨天做了什么、现在是否在线”。

![Star Office UI 预览](docs/screenshots/office-preview-20260301.jpg)

---

## 一、这个项目实现了什么

Star Office UI 目前实现了：

1. **可视化龙虾工作状态**
   - 状态：`idle`（闲置）、`writing`（工作）、`researching`（研究）、`executing`（执行）、`syncing`（同步）、`error`（报 bug）
   - 状态会映射到办公室里的不同区域，并通过动画/气泡展示。

2. **“昨日小记”微型总结**
   - 前端展示“昨日小记”卡片。
   - 后端从 `memory/*.md` 中读取昨天（或最近可用）的记录，做基础脱敏后展示。

3. **支持邀请其他访客加入办公室（功能持续迭代中）**
   - 通过 join key 加入。
   - 访客可持续 push 自己状态到办公室看板。
   - 当前已可用，但整体仍在持续优化交互与接入体验。

4. **已适配手机端访问**
   - 移动端可直接打开与查看状态（适合外出时快速查看）。

5. **公网访问方式灵活**
   - Skill 默认建议使用 Cloudflare Tunnel 快速公网化。
   - 也可以使用你自己的公网域名 / 反向代理方案。

> 你上面列的点是对的，我补充了“状态枚举 + 昨日小记回退机制 + 访客功能仍在迭代”这几项，便于外部开发者理解预期。

---

## 二、本次更新相比上次的主要内容

本次发布相对早期基础版，新增/升级重点如下：

- 新增多 Agent 机制：`/join-agent`、`/agent-push`、`/leave-agent`、`/agents`
- 新增“昨日小记”接口与前端展示：`/yesterday-memo`
- 状态体系更完整：支持 `syncing`、`error` 等状态可视化
- 场景与角色动画升级：补充大量像素动画资产（含访客角色）
- 文档与 Skill 重写：更适合外部程序员快速上手
- 清理发布结构：去除临时文件 / 缓存 / 日志，降低阅读门槛
- 补充开源声明：代码 MIT、但美术资产禁止商用

---

## 三、快速开始

### 1) 安装依赖

```bash
cd star-office-ui
python3 -m pip install -r backend/requirements.txt
```

### 2) 初始化状态文件

```bash
cp state.sample.json state.json
```

### 3) 启动后端

```bash
cd backend
python3 app.py
```

打开：`http://127.0.0.1:18791`

### 4) 切换主 Agent 状态（示例）

```bash
python3 set_state.py writing "正在整理文档"
python3 set_state.py syncing "同步进度中"
python3 set_state.py error "发现问题，排查中"
python3 set_state.py idle "待命中"
```

---

## 四、常用 API

- `GET /health`：健康检查
- `GET /status`：主 Agent 状态
- `POST /set_state`：设置主 Agent 状态
- `GET /agents`：获取多 Agent 列表
- `POST /join-agent`：访客加入
- `POST /agent-push`：访客推送状态
- `POST /leave-agent`：访客离开
- `GET /yesterday-memo`：昨日小记

---

## 五、美术资产使用说明（请务必阅读）

### 访客角色资产来源

访客角色动画使用了 LimeZu 的免费资产：
- **Animated Mini Characters 2 (Platformer) [FREE]**
- https://limezu.itch.io/animated-mini-characters-2-platform-free

请在二次发布/演示时保留来源说明，并遵守原作者许可条款。

### 其他资产来源

- 主角色动画与办公室场景：由本项目作者团队自行制作。

### 商用限制（重要）

- 代码玩法可以基于 MIT 使用与二次开发。
- **本仓库美术资产（含主角色/场景/素材整包）禁止商用。**
- 若你要做商用，请务必制作并替换成你自己的美术资产。

---

## 六、开源许可与声明

- **Code / Logic：MIT**（见 `LICENSE`）
- **Art Assets：非商用，仅学习/演示用途**

欢迎 Fork、交流玩法、提 PR；但请严格遵守资产使用边界。

---

## 七、期待更多玩法交流

欢迎你基于这个框架扩展：
- 更丰富的状态语义与自动编排
- 多房间/多团队协作地图
- 任务看板、时间线、日报自动生成
- 更完整的访问控制与权限体系

如果你做了有趣改造，欢迎分享！

---

## 八、作者社交账号

- **X：Ring Hyacinth (@ring_hyacinth)**  
  https://x.com/ring_hyacinth
- **X：Simon Lee (@simonxxoo)**  
  https://x.com/simonxxoo

---

## 项目结构（简版）

```text
star-office-ui/
  backend/
    app.py
    requirements.txt
    run.sh
  frontend/
    index.html
    join.html
    invite.html
    layout.js
    ...assets
  docs/
    screenshots/
  office-agent-push.py
  set_state.py
  state.sample.json
  join-keys.json
  SKILL.md
  README.md
  LICENSE
```
