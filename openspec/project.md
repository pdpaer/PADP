# 项目上下文

## 项目信息

**项目名称**: PADP (Photonic Automated Design Platform / 光子自动化设计平台)

**Git 仓库**: https://github.com/pdpaer/PADP.git

**项目结构**:
```
/home/ubuntu/gdsfactory_project/
├── PADP/              # PADP项目主目录（代码实现）
│   ├── src/           # 源代码
│   │   ├── components/    # 通用组件库
│   │   ├── designs/        # 设计实现（cpo/npo/ocs/oio）
│   │   ├── utils/          # 工具函数
│   │   └── pdk/            # PDK配置
│   ├── result/        # 设计结果输出（按类型组织）
│   │   ├── cpo/       # CPO设计结果（gds/stl/images/reports/simulation）
│   │   ├── npo/       # NPO设计结果
│   │   ├── ocs/       # OCS设计结果
│   │   └── oio/       # OIO设计结果
│   ├── docs/          # 文档目录
│   ├── tests/         # 测试目录
│   ├── scripts/       # 脚本工具
│   └── config/        # 配置文件
└── openspec/          # OpenSpec规范（与PADP同级，需求与实现分离）
    ├── project.md     # 项目上下文（本文件）
    ├── AGENTS.md      # AI助手工作流程
    ├── specs/         # 当前规范
    └── changes/       # 变更提案
```

## 项目目的

本项目通过 **OpenSpec** 管理的规范驱动开发工作流，使用 **gdsfactory** 进行光子集成电路（PIC）设计。

**项目目标：**
- 基于 gdsfactory 和 OpenSpec 构建结构化的光芯片设计项目框架
- 支持多种设计类型（CPO、NPO、OCS、OIO）的组织和管理
- 使用 gdsfactory 的参数化组件系统设计光子芯片（波导、耦合器、MMI、光栅耦合器等）
- 通过 OpenSpec 管理设计需求、约束和规范
- 保持设计规范（在 `openspec/` 中）与实现代码（在 `PADP/` 中）的清晰分离
- 实现端到端设计流程：设计 → 验证（DRC, LVS）→ 验证（仿真、测试）

**设计类型：**
- **CPO**: Co-Packaged Optics（共封装光学器件）
- **NPO**: Near-Package Optics（近封装光学器件）
- **OCS**: Optical Circuit Switch（光路开关）
- **OIO**: Optical Input/Output（光输入/输出）

**工作流程：**
1. **Vibe编程阶段**：高层规划、愿景设计、系统架构分析
2. **OpenSpec阶段**：规范驱动开发，管理设计需求、任务拆解、变更追踪
3. **gdsfactory实现阶段**：使用 Python 和 gdsfactory 库进行实际的光子芯片版图设计

## 技术栈

### 核心技术
- **Python**: 3.11、3.12 或 3.13（gdsfactory 要求）
- **gdsfactory**: 9.31.0+ - 用于芯片设计的 Python 库（光子、模拟、量子、MEMS）
- **kfactory**: >=2.2,<2.3 - KLayout C++ 库的包装器，用于 GDS 操作

### 关键依赖
- **科学计算**: numpy, scipy, pandas
- **几何处理**: shapely<3, trimesh>=4.4.1
- **可视化**: matplotlib<4
- **数据格式**: pyyaml, orjson<4
- **工具库**: loguru<1, rich<15, typer<1, pydantic>=2

### 开发工具
- **代码质量**: ruff>=0.8.3（代码检查和格式化）
- **测试**: pytest, pytest-cov, pytest-xdist（并行测试）
- **类型检查**: mypy（严格模式）
- **文档**: Google 风格文档字符串

### 可选依赖
- **仿真工具**: Ansys, Lumerical, Tidy3d, MEEP, DEVSIM, SAX, MEOW, Xyce
- **CAD工具**: KLayout, KWeb
- **PDK**: 各种工艺设计包（Cornerstone, SiEPIC, VTT 等）

## 项目约定

### 代码风格
- **格式化工具**: Ruff（通过 `ruff format` 自动格式化）
- **代码检查**: Ruff，包含全面的规则集（参见 `gdsfactory/pyproject.toml`）
- **文档字符串**: Google 风格约定（由 ruff pydocstyle 强制执行）
- **类型提示**: 所有函数都需要类型提示（严格 mypy 检查）
- **行长度**: 灵活（E501 忽略，但建议合理的行长度）
- **导入风格**: 通过 ruff 使用 isort

### 架构模式

**基于组件的设计：**
- 使用 `@gf.cell` 装饰器创建参数化组件
- 组件是可重用的参数化函数，返回 `gf.Component` 对象
- 层次化设计：组件可以引用其他组件
- 基于端口的连接：组件暴露端口用于路由

**设计模式：**
- **参数化组件**: 所有组件接受参数以提高灵活性
- **工厂模式**: 使用 `gf.components.*` 获取标准组件
- **构建器模式**: 在组件上链式操作（例如 `c.add_ref()`, `c.connect()`）
- **截面模式**: 将波导截面定义与路径分开

**文件组织：**
- 组件定义在 `PADP/src/components/`
- 设计实现按类型组织在 `PADP/src/designs/{cpo,npo,ocs,oio}/`
- 技术/PDK 定义在 `PADP/src/pdk/`
- 测试在 `PADP/tests/` 目录
- 版图输出（GDS/STL 文件）在 `PADP/result/{type}/{gds,stl,images,reports,simulation}/`

### 测试策略

**测试框架**: pytest

**测试方法：**
- **组件测试**: 验证组件几何、端口和参数
- **回归测试**: 使用 `pytest_regressions` 捕获不期望的更改
- **并行测试**: 使用 `pytest-xdist` 加快测试执行
- **覆盖率**: 目标是对组件库进行全面覆盖
- **可视化测试**: 可以通过 `c.show()` 可视化检查组件

**测试命令：**
```bash
# 运行所有测试
make test

# 运行覆盖率测试
make cov

# 运行特定测试文件
pytest tests/test_components.py
```

**测试要求：**
- 所有新组件都应该有相应的测试
- 测试应该验证组件参数、端口和几何形状
- 在进行有意的更改时，使用 `--force-regen` 标志更新回归测试数据

### Git 工作流

**分支策略：**
- `main` 分支：稳定、可用于生产的代码
- 功能分支：用于新功能或更改
- 在实现之前遵循 OpenSpec 变更提案工作流

**提交约定：**
- 使用描述性的提交消息
- 在适用时引用 OpenSpec 变更 ID
- 尽可能遵循约定式提交格式

**与 OpenSpec 的集成：**
- 在进行重大更改之前创建 OpenSpec 变更提案
- 实现后归档已完成的更改
- 保持 `openspec/` 和 `PADP/` 的更改同步
- OpenSpec 与 PADP 在同一级目录，保持需求与实现分离

## 领域上下文

### 光子集成电路（PIC）设计

**关键概念：**
- **组件**: 基本构建块（波导、耦合器、MMI、光栅等）
- **端口**: 组件之间的连接点（光学端口）
- **路由**: 通过波导自动或手动连接组件
- **层**: 表示不同材料/工艺的 GDS 层
- **截面**: 定义波导几何形状（宽度、层、包层等）

**常见组件：**
- **波导**: 光信号路径
- **光栅耦合器**: 光纤到芯片的耦合
- **MMI（多模干涉仪）**: 功率分配器/合路器
- **定向耦合器**: 具有特定耦合比的功率分配
- **弯曲**: 具有指定半径的波导弯曲
- **锥形**: 宽度过渡

**设计约束：**
- **最小特征尺寸**: 通常为 0.2 µm 或更小（取决于工艺）
- **弯曲半径**: 避免过度损耗的最小半径（通常 ≥ 10 µm）
- **波导宽度**: 标准宽度（例如，硅光子学为 0.45-0.6 µm）
- **层映射**: GDS 层号映射到物理材料/工艺

**工作流阶段：**
1. **设计**: 使用 gdsfactory 创建参数化组件
2. **版图**: 将组件组装成电路
3. **验证**: DRC（设计规则检查）、LVS（版图与原理图对比）
4. **仿真**: 组件/电路的光学/电学仿真
5. **验证**: 制造后测试和数据分析

### 工艺设计包（PDK）

**可用 PDK：**
- 开源：Cornerstone, SiEPIC Ebeam UBC, VTT, Luxtelligence GF
- 代工厂 PDK：需要 NDA（通过 GDSFactory+ 订阅）

**PDK 组件：**
- 层定义
- 截面定义
- 组件库
- 设计规则
- KLayout 技术文件

## 重要约束

### 技术约束
- **Python 版本**: 必须是 3.11、3.12 或 3.13（gdsfactory 要求）
- **GDS 格式**: 输出 GDSII 或 OASIS 文件用于制造
- **组件验证**: 所有组件必须通过 DRC 和 LVS 检查
- **端口兼容性**: 组件必须具有兼容的端口用于路由

### 设计约束
- **工艺约束**: 遵循 PDK 特定的设计规则
- **性能约束**: 组件必须满足指定的性能指标（损耗、带宽等）
- **制造约束**: 设计必须可制造（最小特征尺寸、对准标记等）

### 工作流约束
- **规范驱动**: 所有新功能在实现之前必须有 OpenSpec 提案
- **关注点分离**: 保持规范（`openspec/`）与实现（`gdsfactory/`）分离
- **版本控制**: OpenSpec 更改和代码更改应该一起跟踪

### 监管/业务约束
- **NDA 要求**: 某些 PDK 需要 NDA 和 GDSFactory+ 订阅
- **许可证**: gdsfactory 使用 MIT 许可证
- **出口管制**: 可能适用于某些光子设计

## 外部依赖

### 核心库
- **gdsfactory**: 主要设计库（作为包依赖安装）
- **kfactory**: KLayout 集成（GDS 操作必需）

### 仿真工具（可选）
- **Ansys**: FDTD 和其他仿真工具
- **Lumerical**: 光子仿真套件
- **Tidy3d**: 3D FDTD 仿真
- **MEEP**: MIT 电磁方程传播
- **SAX**: 光子符号分析
- **MEOW**: 模式求解器
- **DEVSIM**: 器件仿真
- **Xyce**: 电路仿真

### CAD 工具
- **KLayout**: 版图查看器和编辑器（可视化必需）
- **KWeb**: 基于 Web 的 KLayout 界面（可选）

### 开发基础设施
- **Node.js**: >=20.19.0（用于 OpenSpec CLI）
- **OpenSpec CLI**: `@fission-ai/openspec@latest`（用于规范管理）
- **uv**: Python 包管理器（在 Makefile 中使用，可选）

### 外部服务
- **GitHub**: 代码仓库和 CI/CD
- **PyPI**: Python 包分发
- **文档**: 托管在 GitHub Pages
