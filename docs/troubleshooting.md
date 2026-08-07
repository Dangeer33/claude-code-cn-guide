# 踩坑记录（Troubleshooting）

下面按「现象 → 原因 → 解决」记录实操中遇到的问题，供后来人对照避坑。

## 常见问题速查

### npm install 很慢或超时

- **原因**：未配置国内镜像
- **解决**：
  ```powershell
  npm config get registry
  # 应显示 https://registry.npmmirror.com/
  # 若不是，执行：
  npm config set registry https://registry.npmmirror.com/
  ```

### `claude 不是内部命令` / `command not found`

- **原因**：安装后未重开终端，或 PATH 未包含 `.local\bin`
- **解决**：
  - 关闭终端重新打开
  - Windows：检查 `%USERPROFILE%\.local\bin` 是否在 PATH
  - Mac：`echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc`

### `ccr code` 启动后报错（config.json 格式错误）

- **原因**：JSON 多了逗号或少了引号
- **解决**：把 config.json 粘贴到 https://jsonlint.com 校验语法

### API 返回 500：`Cannot read properties of undefined`

- **原因**：config.json 字段大小写或路由格式错误
- **解决**：`Providers` / `Router` 首字母必须大写；路由格式必须是逗号分隔 `siliconflow,deepseek-ai/DeepSeek-V3`

### API 返回 401

- **原因**：API Key 不正确或已过期
- **解决**：去硅基流动控制台确认 Key 状态，必要时重新创建

### PowerShell 禁止运行脚本

- **原因**：Windows 默认安全策略禁止运行脚本
- **解决**：
  ```powershell
  Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
  ```

---

## 我遇到的其他问题

### 安装 claude-code-router 报错：better-sqlite3 原生编译失败（EPERM / find VS）

- **现象**：`npm install -g @musistudio/claude-code-router` 失败，`ccr --help` 提示「不是内部或外部命令」。日志关键报错：
  - `npm error gyp ERR! find VS Could not find any Visual Studio installation`
  - `prebuild-install warn install unable to verify the first certificate`
  - `npm warn cleanup Failed to remove some directories` + `EPERM: operation not permitted`
- **环境**：Windows 11 / Node v24.11.1（Current）/ 国内网络 / npm 镜像 npmmirror
- **原因**：
  1. CCR 依赖 `better-sqlite3`，它需要原生编译或下载预编译二进制
  2. 国内网络存在 TLS 拦截，预编译二进制下载失败（证书校验不过）
  3. npm 回退源码编译，但本机没装 Visual Studio C++ 生成工具 → 编译失败，CCR 没装上
  4. 上次失败的残留目录导致 `EPERM` 清理报错
- **解决**（按推荐顺序）：
  1. 清理残留：`npm uninstall -g @musistudio/claude-code-router`（若仍 EPERM，手动删 `C:\Users\pc\AppData\Roaming\npm\node_modules\@musistudio\claude-code-router`）
  2. 临时绕过证书校验：`npm config set strict-ssl false`（装完改回 `true`）
  3. 重装：`npm install -g @musistudio/claude-code-router`
  4. 若仍要源码编译，安装 VS 生成工具并勾选「使用 C++ 的桌面开发」：https://visualstudio.microsoft.com/zh-hans/visual-cpp-build-tools/
  5. 建议把 Node 换成 LTS v22（Current v24 缺预编译包概率更高）
- **状态**：⚠️ 本机未采用。本项目最终改走「直接运行 `claude`」主路线（见[安装指南](installation.md)）。若你仍想尝试 CCR，可按上述步骤修复；修复成功标志为 `ccr --help` 显示帮助信息。

> 报错日志见 `npm-debug` 日志；成功标志为 `ccr --help` 显示帮助信息。

---

