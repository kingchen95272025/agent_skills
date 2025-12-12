# 第一阶段：收集 (Collect)

## 触发条件

- 用户请求整理某个业务模块的逻辑
- 用户需要了解数据实体关系
- 用户请求评估需求变更的影响

## 阶段目标

全面收集业务相关的数据实体信息、代码实现和表结构，为后续分析提供完整的数据基础。

## 我的工作

### 1. 项目结构识别

快速定位关键目录：

- Model 层目录（如 `app/Models`）
- Service 层目录（如 `app/Services`）
- Repository 层目录（如 `app/Repositories`）
- 枚举类目录（如 `app/Enums`）
- 数据库迁移目录（如 `database/migrations`）

### 2. Model 文件收集

**核心任务**：读取相关业务模块的 Model 文件

收集内容：

- 模型类定义
- 表名映射（`$table`）
- 可填充字段（`$fillable`）
- 类型转换（`$casts`）
- 关联关系方法（`belongsTo`, `hasMany`, `belongsToMany` 等）
- 访问器和修改器
- 作用域方法

示例读取命令：

```
读取 app/Models/Order.php
读取 app/Models/OrderItem.php
读取 app/Models/OrderStatusLog.php
```

### 3. 数据库表结构获取

**方式一：读取迁移文件**

```
读取 database/migrations/*_create_orders_table.php
读取 database/migrations/*_create_order_items_table.php
```

**方式二：使用数据库 MCP（如可用）**

```
获取 orders 表结构
获取 order_items 表结构
```

收集内容：

- 字段名称和类型
- 字段注释（业务含义）
- 索引定义
- 外键关系
- 默认值

### 4. 枚举类收集

读取相关的枚举类，理解业务状态：

```
读取 app/Enums/OrderStatus.php
读取 app/Enums/PaymentStatus.php
```

收集内容：

- 枚举值定义
- 枚举值的业务含义
- 多语言翻译

### 5. Service 层代码收集

理解核心业务逻辑：

```
读取 app/Services/OrderService.php
```

收集内容：

- 核心业务方法
- 业务规则和约束
- 状态流转逻辑
- 与其他模块的交互

### 6. Controller/接口定义收集

了解对外暴露的接口：

```
读取 app/Http/Controllers/Api/OrderController.php
读取 routes/api.php 中的订单相关路由
```

收集内容：

- 接口端点定义
- 请求参数
- 响应结构

## 收集清单模板

收集完成后，整理为以下格式：

```markdown
## 数据实体收集清单

### 1. Model 文件

| 文件路径 | 模型名称 | 对应表 | 说明 |
|----------|----------|--------|------|
| app/Models/Order.php | Order | orders | 订单主表 |
| app/Models/OrderItem.php | OrderItem | order_items | 订单明细 |

### 2. 表结构信息

#### orders 表

| 字段名 | 类型 | 说明 |
|--------|------|------|
| id | bigint | 主键 |
| order_no | varchar | 订单号 |
| ... | ... | ... |

### 3. 枚举类

| 文件路径 | 枚举名称 | 说明 |
|----------|----------|------|
| app/Enums/OrderStatus.php | OrderStatus | 订单状态 |

### 4. 关联关系

| 主模型 | 关联类型 | 关联模型 | 说明 |
|--------|----------|----------|------|
| Order | hasMany | OrderItem | 一个订单有多个明细 |

### 5. 业务逻辑文件

| 文件路径 | 关键方法 | 说明 |
|----------|----------|------|
| app/Services/OrderService.php | createOrder | 创建订单 |
```

## 重要约束

- **全面收集** - 不要遗漏相关的实体和代码
- **记录来源** - 标注每项信息的来源文件
- **保持原样** - 收集阶段只收集，不做分析解读
- **识别边界** - 明确本次整理的范围边界

## 与用户的交互

收集阶段的交互示例：

**开始收集**：

- "好的，让我先收集订单相关的数据实体信息..."
- "我会从 Model、数据库、枚举、Service 等方面全面收集..."

**收集过程中**：

- "我找到了订单相关的 5 个 Model 文件..."
- "正在读取数据库表结构..."
- "发现了 3 个相关的枚举类..."

**收集完成**：

- "数据收集完成，我整理了一份收集清单，接下来进入分析阶段..."

## 完成标志

- Model 文件全部读取
- 表结构信息获取完整
- 枚举类收集完成
- 核心业务代码已读取
- 收集清单整理完毕
- 准备进入分析阶段

## 数据获取优先级

1. **首选**：Model 文件（最准确的业务定义）
2. **次选**：数据库迁移文件（表结构定义）
3. **补充**：数据库 MCP 查询（实时数据结构）
4. **参考**：Service 层代码（业务逻辑实现）

## 常见模块的收集范围

### 订单模块示例

```
Models: Order, OrderItem, OrderStatusLog, OrderPayment
Enums: OrderStatus, PaymentStatus, RefundStatus
Services: OrderService, PaymentService
```

### 用户模块示例

```
Models: User, UserProfile, UserAddress, UserRole
Enums: UserStatus, UserType
Services: UserService, AuthService
```

### 商品模块示例

```
Models: Product, ProductCategory, ProductSku, ProductAttribute
Enums: ProductStatus, ProductType
Services: ProductService, InventoryService
```
