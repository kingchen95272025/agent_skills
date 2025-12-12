# HTML/CSS/JS 代码规范

## HTML规范

### 基本规则
- 使用语义化标签
- 标签必须正确闭合
- 属性使用双引号
- 合理的缩进（2空格或4空格）

### 示例
```html
<div class="container">
    <header class="page-header">
        <h1>页面标题</h1>
    </header>
    <main class="content">
        <!-- 主要内容 -->
    </main>
</div>
```

## CSS规范

### 基本规则
- 使用class选择器，避免过度使用ID选择器
- 遵循BEM命名规范（可选）
- 按功能模块组织CSS
- 合理使用CSS变量

### 示例
```css
/* 基础样式 */
.container {
    max-width: 1200px;
    margin: 0 auto;
}

/* 组件样式 */
.btn-primary {
    background: #E94609;
    color: white;
    padding: 8px 16px;
}
```

## JavaScript规范

### 基本规则
- 使用const/let，避免使用var
- 函数命名使用驼峰命名法
- 添加必要的注释
- 避免全局变量污染

### 示例
```javascript
// 数据定义
const mockData = [];

// 功能函数
function loadData() {
    // 实现逻辑
}
```

## 注释规范

### HTML注释
```html
<!-- 筛选区域 -->
<div class="filter-section">
    <!-- 内容 -->
</div>
```

### JavaScript注释
```javascript
/**
 * 搜索列表
 * @description 根据筛选条件搜索数据
 */
function searchList() {
    // 实现
}
```
