# Claude Code 国内网络配置指南

> 在国内网络环境（无需翻墙）跑通 Claude Code（及类 Agent 框架）的实操记录与经验分享。

## 项目简介

本项目记录了在**国内家庭宽带**环境下从零配置 Claude Code 的完整过程。

原 B 站教程的思路是：用 `claude-code-router`（CCR）把请求转发给国产模型（如 DeepSeek V3 / 硅基流动），实现零成本、国内直连使用 Claude Code。我在跟练时发现 **CCR 在本机因原生编译问题无法安装**（见[踩坑记录](docs/troubleshooting.md)），但意外确认 **Claude Code 本身可以直接运行并使用**（本机实测启动后顶部显示 `mimo-v2.5-pro`，已能正常对话）。

因此本仓库以**两条路线**呈现：

- **主路线（本机实测可用）**：直接安装并运行 `claude`，无需额外路由工具。
- **备选路线（教程原方案，本机未跑通）**：CCR + 硅基流动/DeepSeek，作为参考保留在 [model-setup.md](docs/model-setup.md)，供环境不同的读者尝试。

## 为什么做这个项目

> 想学习 AI Agent 工作流，但苦于国内网络与 Anthropic 账号限制。看完 B 站教程后，把实操经验（包括踩坑）整理成开源项目，既巩固学习，也方便他人避坑。

## 技术架构

**主路线（已验证）：**

```
┌─────────────┐      ┌─────────────────────────┐
│  Claude Code │ ──> │  本机已有的可用模型配置     │
│  (Agent 框架)│      │  (如 mimo-v2.5-pro)       │
└─────────────┘      └─────────────────────────┘
      本地终端                 国内直连
```

**备选路线（教程原方案，需额外安装 CCR）：**

```
┌─────────────┐     ┌──────────────────────┐     ┌──────────────────┐
│  Claude Code │ ──> │  claude-code-router  │ ──> │  硅基流动 / DeepSeek │
│  (Agent 框架)│     │  (本地请求转发代理)    │     │  (国产模型 API)     │
└─────────────┘     └──────────────────────┘     └──────────────────┘
      本地终端            127.0.0.1:端口              国内直连
```

## 快速开始（主路线，已验证）

```bash
# 1. 安装 Node.js（建议 LTS）并配置国内 npm 镜像
npm config set registry https://registry.npmmirror.com/

# 2. 安装 Claude Code
npm install -g @anthropic-ai/claude-code

# 3. 直接运行
claude
```

> 若 `claude` 启动后有可用模型并能对话，即配置完成。本机实测显示模型为 `mimo-v2.5-pro`，配置过程见[安装指南](docs/installation.md)。

## 文档导航

| 文档                                 | 内容                                   |
| ---------------------------------- | ------------------------------------ |
| [安装指南](docs/installation.md)       | Node.js / Git / Python / Claude Code 分步安装，含截图（**主路线**） |
| [模型接入（可选）](docs/model-setup.md) | CCR + 硅基流动备选方案，本机未跑通，仅供参考          |
| [踩坑记录](docs/troubleshooting.md)    | 实操中遇到的报错与解决方案（含 CCR 安装失败案例）        |
| [配置示例](config/config.example.json) | 备选路线用的脱敏 config.json 示例              |

## 适用环境

- 操作系统：Windows 10/11（已实测）；macOS / Linux 参见视频原教程
- Node.js：v18+（建议 LTS；本机为 v24.11.1 Current）
- 网络：中国大陆家庭宽带

## 致谢

- 视频教程：[从零开始用国内网络，跑通 Claude Code 等一切 Agent](https://b23.tv/L8CRo2H)
- 备选模型方案：硅基流动 (SiliconFlow) / DeepSeek

## 免责声明

本项目仅用于学习与技术交流。请遵守各平台服务条款，API Key 等敏感信息请妥善保管，切勿上传至公开仓库。
