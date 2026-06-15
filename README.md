# MWRT — ImmortalWrt 固件构建配置

个人自用的 ImmortalWrt 固件编译 CI/CD 配置仓库。本仓库不包含应用程序源代码，仅包含构建配置和设备初始化脚本。

## 仓库结构

- `diffconfig` — main 构建的 Kconfig 片段
- `diffconfig-2512` — 2512 构建的 Kconfig 片段（含 Lua/LuCI 兼容层）
- `99-custom` — UCI 默认设置（root 密码、LAN IP 等）
- `99-setip` — 交互式 LAN IP 更改工具
- `.github/workflows/` — GitHub Actions 工作流

## 使用方法

所有构建通过 GitHub Actions 自动完成，无需本地编译。在 Actions 选项卡中选择工作流，点击 "Run workflow" 手动触发构建。构建完成后在 artifact 中下载固件。

## 配置说明

使用前需配置以下 GitHub 配置：

**Secrets:**
- `LOGIN_PASSWD` — 设备注入的 root 密码

**Variables:**
- `IMM_TAG` — main 构建的 ImmortalWrt 标签
- `IMM_2512_TAG` — 2512 构建的 ImmortalWrt 标签

