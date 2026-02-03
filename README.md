# Vitis-HLS-Scripts-

## 项目简介

本仓库提供了 Vitis HLS 的自动化构建脚本，使用 Makefile 配合 TCL 脚本，自动编译 Vitis HLS 源文件并导出 IP 核心。支持 Vitis HLS 2024 和 2025 两个版本。

## 目录结构

```
.
├── 2024 edition/     # 适用于 Vitis HLS 2024 版本的构建脚本
│   ├── Makefile      # Make 构建文件
│   └── build.tcl     # TCL 构建脚本
├── 2025 edition/     # 适用于 Vitis HLS 2025 版本的构建脚本
│   ├── Makefile      # Make 构建文件
│   └── build.tcl     # TCL 构建脚本
└── README.md         # 本文件
```

## 主要功能

### 自动化构建流程
- 自动检测当前目录下的 `.cpp` 文件作为 IP 核心源文件
- 支持批量构建多个 IP 核心
- 支持单独构建指定的 IP 核心
- 自动化的增量构建，仅在源文件更新时重新构建

### 灵活的配置选项
- **TARGET**: 构建目标（ip/syn/sim/cosim/all），默认为 ip
- **DEVICE**: 目标器件型号，默认为 xcv80-lsva4737-2MHP-e-S
- **FREQ**: 目标时钟频率（MHz），默认为 300MHz（仅 2024 版本）
- **IP**: 指定要构建的 IP 核心名称

## 使用方法

### 准备工作
1. 将需要的版本文件夹（`2024 edition` 或 `2025 edition`）中的 `Makefile` 和 `build.tcl` 复制到您的 HLS 项目目录
2. 确保项目目录中包含 `.cpp` 源文件
3. 确保已安装并配置好 Vitis HLS 环境

### 基本命令

```bash
# 构建所有检测到的 IP 核心
make

# 构建特定的 IP 核心
make <ip_name>
make IP=<ip_name>

# 指定目标器件
make DEVICE=xcv80-lsva4737-2MHP-e-S

# 指定时钟频率（仅 2024 版本）
make FREQ=300

# 指定构建目标
make TARGET=ip      # 综合并导出 IP 核心（默认）
make TARGET=syn     # 仅进行综合
make TARGET=sim     # 仅进行 C 仿真
make TARGET=cosim   # 进行协同仿真
make TARGET=all     # 执行所有流程

# 清理构建文件
make clean

# 强制重新构建所有
make rebuild

# 强制重新构建特定 IP
make force-<ip_name>

# 刷新特定 IP（清理并重新构建）
make refresh-<ip_name>

# 刷新所有 IP
make refresh

# 列出可用的 IP 核心
make list

# 显示帮助信息
make help
```

### 使用示例

```bash
# 示例 1: 构建所有 IP，使用默认设置
make

# 示例 2: 构建名为 "filter" 的 IP，指定器件和频率
make filter DEVICE=xcv80-lsva4737-2MHP-e-S FREQ=400

# 示例 3: 综合并导出 IP
make TARGET=ip IP=filter

# 示例 4: 执行完整流程（仿真、综合、协同仿真、导出）
make TARGET=all

# 示例 5: 清理后重新构建
make clean
make
```

## 版本差异

### 2024 Edition
- 使用 `vitis_hls` 命令调用 TCL 脚本
- 通过命令行参数传递配置（TARGET、DEVICE、IPNAME、FREQ）
- 支持手动指定时钟频率

### 2025 Edition
- 使用 `vitis-run --mode hls` 命令调用 TCL 脚本
- 通过环境变量传递配置（TARGET、DEVICE、IPNAME）
- 使用固定时钟周期 3.33ns（约 300MHz）
- 使用 `-reset` 选项确保干净构建

## 技术细节

### 构建流程
1. 自动检测目录中的 `.cpp` 文件
2. 为每个 IP 创建独立的构建目录 `build_<ipname>.<device>`
3. 调用 Vitis HLS 执行 TCL 脚本
4. 根据 TARGET 参数执行相应的构建步骤：
   - **sim**: C 仿真
   - **syn**: C 综合
   - **ip**: C 综合 + 导出 IP
   - **cosim**: C 综合 + 协同仿真
   - **all**: 完整流程

### 接口配置
- 启用 64 位 AXI 地址 (`config_interface -m_axi_addr64=true`)
- 导出格式为 IP Catalog (`config_export -format ip_catalog`)
- C++ 标准: C++14

## 许可证

本项目使用 MIT 许可证。版权所有 (c) 2025 Advanced Micro Devices, Inc.

## 贡献

欢迎提交问题和拉取请求来改进这些脚本。