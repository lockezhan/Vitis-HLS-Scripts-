# Vitis HLS 构建脚本

适用于使用 Makefile 配合 TCL 脚本，自动化编译 Vitis HLS 文件并导出 IP 核心的构建系统。

## 简介

本仓库提供了两个版本的 Vitis HLS 自动化构建脚本，分别适配不同版本的 Vitis 工具：

- **2024 edition**：适用于 Vitis HLS 2024.x 版本
- **2025 edition**：适用于 Vitis 2025.1 版本（采用新的 `vitis-run` 命令和语法优化）

两个版本均提供了完整的 Makefile 和 TCL 脚本，支持自动检测 C++ 源文件、灵活配置器件参数、多种构建目标，以及便捷的管理命令。

## 主要特性

### 2024 版本特性

- 使用传统的 `vitis_hls` 命令调用 TCL 脚本
- 支持命令行参数传递（TARGET、DEVICE、IP名称、频率）
- 自动管理构建目录，根据源文件变化自动清理
- 支持多种构建目标：`ip`、`syn`、`sim`、`cosim`、`all`
- 可配置的目标频率（默认 300 MHz）

### 2025 版本特性

- 采用新的 `vitis-run --mode hls` 命令
- 通过环境变量传递参数，更符合 Vitis 2025.1 的规范
- 优化的项目管理，使用 `-reset` 选项确保干净构建
- 改进的配置导出选项，支持自定义显示名称

## 使用方法

### 基本使用

1. 将对应版本目录中的 `Makefile` 和 `build.tcl` 复制到你的 HLS 项目目录
2. 确保项目目录中有 `.cpp` 源文件
3. 运行构建命令：

```bash
# 构建所有检测到的 IP 核心
make

# 构建指定的 IP 核心
make <ip_name>

# 或者使用 IP 参数
make IP=<ip_name>

# 查看帮助信息
make help

# 列出可用的 IP 核心
make list
```

### 高级配置

```bash
# 指定目标器件
make DEVICE=xcv80-lsva4737-2MHP-e-S

# 指定目标频率（仅 2024 版本支持通过 Makefile 传递）
make FREQ=300

# 指定构建目标
make TARGET=ip      # 综合并导出 IP（默认）
make TARGET=syn     # 仅综合
make TARGET=sim     # 仅仿真
make TARGET=cosim   # C/RTL 协同仿真
make TARGET=all     # 执行所有步骤

# 强制重新构建
make rebuild              # 重新构建所有
make force-<ip_name>      # 强制重新构建指定 IP
make refresh-<ip_name>    # 刷新指定 IP
```

### 清理构建

```bash
# 清理所有构建目录
make clean
```

## 版本差异

| 特性 | 2024 版本 | 2025 版本 |
|------|----------|----------|
| 调用命令 | `vitis_hls build.tcl -tclargs ...` | `vitis-run --mode hls --tcl build.tcl` |
| 参数传递 | 命令行参数 | 环境变量 |
| 项目打开 | `open_project` | `open_project -reset` |
| Solution 管理 | 手动删除 sol1 目录 | `open_solution -reset` |
| 频率配置 | 命令行参数传递 | TCL 脚本内硬编码（3.33ns = 300MHz） |
| 导出配置 | 基础配置 | 支持更多选项（display_name、rtl 格式等） |

## 许可证

MIT License - Copyright (c) 2025 Advanced Micro Devices, Inc.

## 注意事项

- 确保已安装对应版本的 Vitis HLS 工具
- 构建前请确保 C++ 源文件符合 HLS 综合要求
- 默认使用 C++14 标准编译
- 2025 版本需要 Vitis 2025.1 或更高版本
