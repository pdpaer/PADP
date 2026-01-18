# PADP - Photonic Automated Design Platform

光子自动化设计平台

## 项目简介

PADP (Photonic Automated Design Platform) 是一个基于 gdsfactory 和 OpenSpec 的结构化光芯片设计项目框架，支持多种设计类型的组织和管理。

## 设计类型

- **CPO**: Co-Packaged Optics（共封装光学器件）
- **NPO**: Near-Package Optics（近封装光学器件）
- **OCS**: Optical Circuit Switch（光路开关）
- **OIO**: Optical Input/Output（光输入/输出）

## 项目结构

```
PADP/
├── src/                      # 源代码目录
│   ├── components/           # 通用组件库
│   ├── designs/              # 设计实现（cpo/npo/ocs/oio）
│   ├── utils/                # 工具函数
│   └── pdk/                  # PDK配置
├── result/                   # 设计结果输出（按类型组织）
├── docs/                     # 文档目录
├── tests/                    # 测试目录
├── scripts/                  # 脚本工具
└── config/                  # 配置文件
```

## 快速开始

### 安装依赖

```bash
pip install -r requirements.txt
```

### 使用示例

```python
import gdsfactory as gf
from src.components import waveguides, couplers

# 创建组件
c = gf.Component("my_design")
# ... 设计代码 ...
```

## 开发指南

详细文档请参考 `docs/` 目录。

## 许可证

[待添加]

## 贡献

[待添加]
