# 模型接入配置（可选 · CCR 备选方案）

> ⚠️ **本页为视频原方案的备选路线，本机未能成功安装，仅供环境不同的读者参考。**
>
> 本项目的**主路线**是直接运行 `claude`（见[安装指南 · 第五步](installation.md)），已实测可用。本页的 CCR + 硅基流动方案按 B 站教程保留，但我在 Windows 11 / Node v24 环境下安装时因 `better-sqlite3` 原生编译失败未能跑通（详见[踩坑记录](troubleshooting.md)）。如果你也想尝试此方案，可按以下步骤操作。

## 第一步：注册硅基流动并获取 API Key

1. 打开 https://cloud.siliconflow.cn
2. 右上角登录，用手机号（+86）注册
3. 左侧菜单「API 密钥」→「新建密钥」
4. 复制并保存 Key（格式 `sk-xxxxxxxx`）

> ⚠️ **API Key 只会显示一次！** 立即复制到记事本保存。
> 截图：密钥创建页面（记得打码 Key 值）。

注册送 ¥14 免费额度（约 2000 万 tokens），零成本起步。

---

## 第二步：安装 claude-code-router (CCR)

```powershell
npm install -g @musistudio/claude-code-router
ccr --help
```

> 看到帮助信息即安装成功。若安装失败（原生编译 / 证书问题），见[踩坑记录](troubleshooting.md)。

---

## 第三步：编写配置文件

创建配置文件目录与文件：

```powershell
New-Item -ItemType Directory -Path "$env:USERPROFILE\.claude-code-router" -Force
notepad "$env:USERPROFILE\.claude-code-router\config.json"
```

粘贴以下内容，**把 `sk-xxx` 替换为你的真实 Key**：

```json
{
  "Providers": [
    {
      "name": "siliconflow",
      "api_base_url": "https://api.siliconflow.cn/v1/chat/completions",
      "api_key": "sk-xxx",
      "models": ["deepseek-ai/DeepSeek-V3"],
      "transformer": {
        "use": [
          ["maxtoken", { "max_tokens": 16384 }]
        ]
      }
    }
  ],
  "Router": {
    "default": "siliconflow,deepseek-ai/DeepSeek-V3",
    "think": "siliconflow,deepseek-ai/DeepSeek-V3"
  }
}
```

### 字段说明

| 字段 | 含义 |
|------|------|
| `api_base_url` | 硅基流动 API 地址 |
| `api_key` | 你的 API Key |
| `models` | 使用的模型，`deepseek-ai/DeepSeek-V3` 编程能力强 |
| `transformer.maxtoken` | 限制单次最大输出长度 |
| `Router.default` | 默认模型，格式 `provider名,模型名` |
| `Router.think` | 深度思考时使用的模型 |

> 脱敏示例见 [config/config.example.json](../../config/config.example.json)

---

## 第四步：启动并验证

进入任意项目目录：

```powershell
cd 你的项目目录
ccr code
```

应看到：
1. CCR 启动，显示本地代理地址 `http://127.0.0.1:xxxx`
2. Claude Code 自动启动
3. 出现 `claude>` 提示符

---

## 进阶：其他模型选择

| 方案 | 特点 | 获取 |
|------|------|------|
| 火山引擎 · 豆包编程 | 字节出品，专为 AI 编程优化 | volcengine.com |
| DeepSeek 官方 API | 价格极低，支持推理模型 | platform.deepseek.com |
| API 中转站 | 用真正的 Claude 模型，按量付费 | 搜索「Claude Code 中转站」 |
