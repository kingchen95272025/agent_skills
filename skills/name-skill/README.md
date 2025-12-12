# Name Skill - 编程命名助手

这是一个专门用于编程命名的 Claude Code Skill，帮助您生成专业、一致、符合项目语境的英文命名。

## Skill 结构

```
name-skill/
├── SKILL.md      # 主文件：核心工作流程和命名规范
└── README.md     # 本文件：使用说明
```

## 何时使用这个 Skill

当你需要：

- 为变量、函数、类命名
- 为数据库表、字段命名
- 为 API 路由、参数命名
- 为文件、目录、模块命名
- 确保命名与项目风格一致

## 核心特点

1. **上下文感知** - 分析项目现有命名风格和术语
2. **专业规范** - 遵循各类命名规范（camelCase、snake_case等）
3. **多选项输出** - 提供推荐命名和备选方案
4. **理由说明** - 解释每个命名选择的依据

## 输出语言

**始终输出英文命名**，但解释说明使用中文。

## 支持的命名类型

| 类型 | 风格 | 示例 |
|------|------|------|
| 变量/函数 | camelCase | `orderStatus` |
| 类/接口 | PascalCase | `OrderService` |
| 常量 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 数据库表/字段 | snake_case | `purchase_orders` |
| API路由 | kebab-case | `/purchase-orders` |

## 使用示例

```
用户：需要一个变量来存储用户选中的订单列表

Claude：
📦 命名对象: 变量 (Array)
📝 业务含义: 用户选中的订单列表

✅ 推荐命名: selectedOrders
   理由: 简洁明确，selected表示状态，orders表示复数

📋 备选方案:
   1. checkedOrders
   2. selectedOrderList
```

## 工作流程

1. **上下文理解** - 扫描项目，收集现有术语
2. **需求确认** - 确认命名对象类型和业务含义
3. **命名生成** - 提供推荐命名和备选方案
