# 代码设计示例

本文档提供 Kiro 工作流第二阶段（架构设计）中各层次代码的详细设计示例。这些示例展示了如何编写符合规范的代码设计文档。

## 模型层 (Model) 设计示例

模型层必须包含完整的PHPDoc注释模板：

```php
/**
 * 样品主表模型
 *
 * @property int                                    $id                   主键ID
 * @property string                                 $sample_code          样品编码
 * @property string                                 $sample_name          样品名称
 * @property int                                    $sample_type          样品类型
 * @property int                                    $sample_status        样品状态
 * @property array                                  $sample_images_array  样品图片数组
 *
 * @property-read Collection|SampleVersion[]        $versions             版本列表
 * @property-read SampleStock                       $currentStock         当前库存
 *
 * @method static Builder|static query()
 * @method static Builder|static byIds($ids)
 * @method static Builder|static bySampleCodeIn(array $sampleCodeList)
 * @method static Builder|static bySampleNameLike(string $sampleName)
 */
class Sample extends BaseModelV2
{
    protected $table = 'samples';
    protected $guarded = [];

    protected $casts = [
        'sample_images' => 'array',
        'created_at' => 'datetime:Y-m-d H:i:s',
    ];

    // 获取器和设置器方法
    public function getOperationDataArrayAttribute(): array
    public function setOperationDataArrayAttribute(array $data): void

    // 关联关系定义
    public function application(): BelongsTo
    public function applicationDetail(): BelongsTo
    public function sample(): BelongsTo
    public function version(): BelongsTo

    // 查询作用域方法
    public function scopeByLogTypeIn(Builder $query, array $logTypeList): Builder
    public function scopeByApplicationIdIn(Builder $query, array $applicationIdList): Builder
    public function scopeBySampleIdIn(Builder $query, array $sampleIdList): Builder
    public function scopeByOperatorIdIn(Builder $query, array $operatorIdList): Builder
    public function scopeByOperationTypeIn(Builder $query, array $operationTypeList): Builder
}
```

**模型层设计要点：**

- 必须使用完整的PHPDoc注释，包括@property、@property-read、@method等
- 清晰列出所有数据库字段的@property注释
- 列出所有关联关系的@property-read注释
- 列出所有查询作用域的@method注释
- 定义获取器、设置器、关联关系和查询作用域方法签名，不要写具体的代码实现

## 服务层 (Service) 设计示例

每个业务方法必须包含详细的逻辑步骤注释，不要写具体的代码实现

```php
/**
 * 样品借取服务类
 */
class SampleBorrowService
{
    public function __construct(
        private BorrowApplicationRepository $applicationRepository,
        private ApplicationDetailRepository $detailRepository,
        private SampleRepository $sampleRepository,
        private SampleStockRepository $stockRepository,
        private SampleFlowLogRepository $logRepository,
        private UserRepository $userRepository
    ) {}

    /**
     * 创建借取申请
     *
     * 逻辑描述：
     * 1. 验证用户身份和供应商信息
     * 2. 验证申请数据完整性（申请类型、借用目的、归还日期等）
     * 3. 验证归还日期范围（3-30天）
     * 4. 验证申请明细数据有效性
     * 5. 检查每个样品的库存充足性
     * 6. 生成申请单号（BA+年月日+4位序号）
     * 7. 创建申请主记录和明细记录
     * 8. 根据申请类型和数量确定审批级别
     * 9. 记录申请创建日志
     * 10. 发送申请提交通知给审批人
     *
     * @param array $params 申请数据
     * @param int $userId 申请人ID
     * @return BorrowApplication
     * @throws BusinessException
     */
    public function create(array $params, int $userId): BorrowApplication

    /**
     * 审批借取申请
     *
     * 逻辑描述：
     * 1. 验证审批人权限
     * 2. 检查申请状态是否允许审批
     * 3. 验证审批操作（通过/拒绝）
     * 4. 如果审批通过：
     *    4.1 检查是否需要上级审批
     *    4.2 如果不需要，更新申请状态为已通过
     *    4.3 如果需要，推送到下一审批级别
     * 5. 如果审批拒绝：
     *    5.1 更新申请状态为已拒绝
     *    5.2 释放已锁定的库存
     * 6. 记录审批日志
     * 7. 发送审批结果通知
     *
     * @param int $applicationId 申请ID
     * @param array $params 审批数据
     * @param int $approverId 审批人ID
     * @return BorrowApplication
     * @throws BusinessException
     */
    public function approve(int $applicationId, array $params, int $approverId): BorrowApplication
}
```

**服务层设计要点：**

- 必须包含详细的逻辑步骤注释，每个步骤都要清晰描述
- 复杂逻辑需按小点分步骤描述清楚
- 使用层次化的步骤编号（1、2、3 和 1.1、1.2、1.3）
- 包含完整的参数说明和返回值说明
- 明确可能抛出的异常
- 逻辑注释要详细到可以直接转化为代码实现

## 仓储层 (Repository) 设计示例

必须包含核心方法的完整签名，不要写具体的代码实现

```php
/**
 * 样品仓储类
 */
class SampleRepository extends BaseRepository
{
    /**
     * 构造查询条件
     *
     * @param array $params 查询参数
     * @param array $sort 排序条件
     * @return Builder
     */
    public function buildQuery(array $params, array $sort = ['id' => 'desc']): Builder

    /**
     * 根据ID查找单个记录
     *
     * @param int $id 记录ID
     * @param array $columns 需要查询的字段
     * @return Sample|null
     */
    public function findById(int $id, array $columns = ['*']): ?Sample

    /**
     * 根据ID集合获取多条记录
     *
     * @param array $ids ID数组
     * @param array $columns 需要查询的字段
     * @return Collection
     */
    public function getByIds(array $ids, array $columns = ['*']): Collection

    /**
     * 根据样品编码查找记录
     *
     * @param string $sampleCode 样品编码
     * @return Sample|null
     */
    public function findBySampleCode(string $sampleCode): ?Sample

    /**
     * 分页查询样品列表
     *
     * @param array $params 查询参数
     * @param int $page 页码
     * @param int $pageSize 每页数量
     * @return array [data, total]
     */
    public function paginate(array $params, int $page, int $pageSize): array

    /**
     * 批量创建样品记录
     *
     * @param array $dataList 样品数据数组
     * @return bool
     */
    public function batchCreate(array $dataList): bool

    /**
     * 更新样品状态
     *
     * @param int $id 样品ID
     * @param int $status 新状态
     * @return bool
     */
    public function updateStatus(int $id, int $status): bool
}
```

**仓储层设计要点：**

- 列出所有数据访问方法的完整签名，不要写具体的代码实现
- 包含详细的参数和返回值说明
- 方法名要体现其功能（find/get/create/update/delete等）
- 明确查询、创建、更新、删除等操作的方法

## 控制器层 (Controller) 设计示例

必须包含标准的请求→验证→服务调用→响应返回模式：

```php
/**
 * 样品库存控制器
 */
class SampleStockController extends Controller
{
    /**
     * 库存列表
     *
     * @param SampleStockListRequest $request
     * @param SampleStockService $service
     * @return JsonResponse
     */
    public function list(SampleStockListRequest $request, SampleStockService $service): JsonResponse

    /**
     * 库存详情
     *
     * @param SampleStockDetailRequest $request
     * @param SampleStockService $service
     * @return JsonResponse
     */
    public function details(SampleStockDetailRequest $request, SampleStockService $service): JsonResponse

    /**
     * 创建库存记录
     *
     * @param SampleStockCreateRequest $request
     * @param SampleStockService $service
     * @return JsonResponse
     */
    public function create(SampleStockCreateRequest $request, SampleStockService $service): JsonResponse

    /**
     * 更新库存记录
     *
     * @param SampleStockUpdateRequest $request
     * @param SampleStockService $service
     * @return JsonResponse
     */
    public function update(SampleStockUpdateRequest $request, SampleStockService $service): JsonResponse

    /**
     * 删除库存记录
     *
     * @param SampleStockDeleteRequest $request
     * @param SampleStockService $service
     * @return JsonResponse
     */
    public function delete(SampleStockDeleteRequest $request, SampleStockService $service): JsonResponse

    /**
     * 导出库存数据
     *
     * @param SampleStockExportRequest $request
     * @param SampleStockService $service
     * @return mixed
     */
    public function export(SampleStockExportRequest $request, SampleStockService $service)
}
```

**控制器层设计要点：**

- 每个方法都要有请求验证类和服务类注入
- 方法名遵循标准：list、details、create、update、delete、export、import
- 返回类型统一为JsonResponse（导出除外）
- 不包含具体的业务逻辑实现，只负责调度

## 资源转换层 (Resource) 设计示例

必须包含类型转换、枚举值处理、关联数据处理：

```php
/**
 * 样品资源转换类
 */
class SampleResource extends Resource
{
    public function toArray($request)
    {
        return [
            'id'                    => (int)$this->resource->id,
            'sample_code'           => (string)$this->resource->sample_code,
            'sample_name'           => (string)$this->resource->sample_name,
            'sample_type'           => (int)$this->resource->sample_type,
            'sample_type_name'      => SampleTypeEnum::getTitle($this->resource->sample_type),
            'sample_status'         => (int)$this->resource->sample_status,
            'sample_status_name'    => SampleStatusEnum::getTitle($this->resource->sample_status),
            'total_quantity'        => (int)$this->resource->total_quantity,
            'available_quantity'    => (int)$this->resource->available_quantity,
            'sample_images'         => (array)$this->resource->sample_images_array,
            'supplier_id'           => (int)$this->resource->supplier_id,
            'supplier_name'         => (string)$this->resource->supplier?->name,
            'created_at'            => (string)$this->resource->created_at,
            'updated_at'            => (string)$this->resource->updated_at,

            // 关联数据
            'versions'              => SampleVersionResource::collection($this->resource->versions ?? collect()),
            'current_stock'         => $this->resource->currentStock ? new SampleStockResource($this->resource->currentStock) : null,
        ];
    }
}

/**
 * 样品列表资源转换类（简化版）
 */
class SampleListResource extends Resource
{
    public function toArray($request)
    {
        return [
            'id'                    => (int)$this->resource->id,
            'sample_code'           => (string)$this->resource->sample_code,
            'sample_name'           => (string)$this->resource->sample_name,
            'sample_status'         => (int)$this->resource->sample_status,
            'sample_status_name'    => SampleStatusEnum::getTitle($this->resource->sample_status),
            'total_quantity'        => (int)$this->resource->total_quantity,
            'available_quantity'    => (int)$this->resource->available_quantity,
            'created_at'            => (string)$this->resource->created_at,
        ];
    }
}
```

**资源转换层设计要点：**

- 所有字段都要进行强制类型转换
- 枚举值字段要同时返回值和对应的名称（使用Enum::getTitle()）
- 关联数据使用嵌套的Resource类进行转换
- 区分详情Resource和列表Resource，列表版本通常是简化版
- 处理可能为null的关联数据

## 枚举类 (Enum) 设计示例

必须严格按照值-描述成对定义的格式：

```php
<?php

namespace App\Enums\Sample;

use App\Enums\BaseEnum;

/**
 * 样品状态枚举
 */
class SampleStatusEnum extends BaseEnum
{
    const DEVELOPING = 1;
    const DEVELOPING_TITLE = '开发中';

    const SAMPLING = 2;
    const SAMPLING_TITLE = '打样中';

    const TESTING = 3;
    const TESTING_TITLE = '测试中';

    const APPROVED = 4;
    const APPROVED_TITLE = '已批准';

    const IN_PRODUCTION = 5;
    const IN_PRODUCTION_TITLE = '生产中';

    const DISCONTINUED = 6;
    const DISCONTINUED_TITLE = '已停产';
}
```

```php
<?php

namespace App\Enums\Sample;

use App\Enums\BaseEnum;

/**
 * 样品类型枚举
 */
class SampleTypeEnum extends BaseEnum
{
    const FABRIC = 1;
    const FABRIC_TITLE = '面料样';

    const ACCESSORY = 2;
    const ACCESSORY_TITLE = '辅料样';

    const GARMENT = 3;
    const GARMENT_TITLE = '成衣样';

    const PACKAGING = 4;
    const PACKAGING_TITLE = '包装样';
}
```

**枚举类设计要点：**

- 值和描述成对定义：VALUE 和 VALUE_TITLE
- 业务异常码使用数字
- 严禁出现硬编码，所有固定值都要定义枚举
- 新增枚举类时必须在所有语言包的`new_enums.php`中添加翻译

## 请求验证 (Request) 设计示例

必须包含完整的验证规则和错误消息定义：

```php
<?php

namespace App\Http\Requests\Sample;

use App\Http\Requests\BaseRequest;
use App\Enums\Sample\SampleStatusEnum;
use App\Enums\Sample\SampleTypeEnum;

/**
 * 样品列表请求验证
 */
class SampleListRequest extends BaseRequest
{
    public function myRules(): array
    {
        return [
            'sample_code'        => 'string|max:32',
            'sample_name'        => 'string|max:255',
            'sample_status'      => 'integer|in:' . implode(',', SampleStatusEnum::toArray()),
            'sample_type_list'   => 'array',
            'sample_type_list.*' => 'integer|in:' . implode(',', SampleTypeEnum::toArray()),
            'supplier_id'        => 'integer|min:1',
            'created_start'      => 'string|date_format:Y-m-d',
            'created_end'        => 'string|date_format:Y-m-d',
            'page'               => 'integer|min:1',
            'page_size'          => 'integer|min:1|max:100',
        ];
    }

    public function myMessages(): array
    {
        return [
            'sample_code.string'         => __t('sample.样品编码必须为字符串'),
            'sample_code.max'            => __t('sample.样品编码长度不能超过32字符'),
            'sample_name.string'         => __t('sample.样品名称必须为字符串'),
            'sample_name.max'            => __t('sample.样品名称长度不能超过255字符'),
            'sample_status.integer'      => __t('sample.样品状态必须为整数'),
            'sample_status.in'           => __t('sample.样品状态值不合法'),
            'sample_type_list.array'     => __t('sample.样品类型列表必须为数组'),
            'sample_type_list.*.integer' => __t('sample.样品类型必须为整数'),
            'sample_type_list.*.in'      => __t('sample.样品类型值不合法'),
            'supplier_id.integer'        => __t('sample.供应商ID必须为整数'),
            'supplier_id.min'            => __t('sample.供应商ID必须大于0'),
            'created_start.date_format'  => __t('sample.开始时间格式不正确'),
            'created_end.date_format'    => __t('sample.结束时间格式不正确'),
            'page.integer'               => __t('sample.页码必须为整数'),
            'page.min'                   => __t('sample.页码必须大于0'),
            'page_size.integer'          => __t('sample.每页数量必须为整数'),
            'page_size.min'              => __t('sample.每页数量必须大于0'),
            'page_size.max'              => __t('sample.每页数量不能超过100'),
        ];
    }
}
```

```php
<?php

namespace App\Http\Requests\Sample;

use App\Http\Requests\BaseRequest;
use App\Enums\Sample\SampleTypeEnum;

/**
 * 样品创建请求验证
 */
class SampleCreateRequest extends BaseRequest
{
    public function myRules(): array
    {
        return [
            'sample_code'        => 'required|string|max:32|unique:samples,sample_code',
            'sample_name'        => 'required|string|max:255',
            'sample_type'        => 'required|integer|in:' . implode(',', SampleTypeEnum::toArray()),
            'supplier_id'        => 'required|integer|min:1|exists:suppliers,id',
            'total_quantity'     => 'required|integer|min:1',
            'sample_images'      => 'array',
            'sample_images.*'    => 'string|url',
            'description'        => 'string|max:1000',
        ];
    }

    public function myMessages(): array
    {
        return [
            'sample_code.required'       => __t('sample.样品编码不能为空'),
            'sample_code.unique'         => __t('sample.样品编码已存在'),
            'sample_name.required'       => __t('sample.样品名称不能为空'),
            'sample_type.required'       => __t('sample.样品类型不能为空'),
            'sample_type.in'             => __t('sample.样品类型值不合法'),
            'supplier_id.required'       => __t('sample.供应商ID不能为空'),
            'supplier_id.exists'         => __t('sample.供应商不存在'),
            'total_quantity.required'    => __t('sample.总数量不能为空'),
            'total_quantity.min'         => __t('sample.总数量必须大于0'),
            'sample_images.array'        => __t('sample.样品图片必须为数组'),
            'sample_images.*.url'        => __t('sample.样品图片地址格式不正确'),
            'description.max'            => __t('sample.描述长度不能超过1000字符'),
        ];
    }
}
```

**请求验证设计要点：**

- 必须包含完整的验证规则
- 必须为每个规则提供对应的错误消息
- 使用枚举类来验证枚举值
- 区分必填和可选字段
- 包含字符串长度、数值范围、数组元素等验证

## 路由 (Route) 设计示例

必须按功能模块分组，遵循标准命名：

```php
// routes/api.php

// 样品模块路由组
Route::group(['prefix' => 'sample', 'middleware' => ['auth', 'signature']], function () {
    // 样品归还管理
    Route::group(['prefix' => 'return'], function () {
        Route::post('list', 'Sample\SampleReturnController@list');
        Route::post('details', 'Sample\SampleReturnController@details');
        Route::post('create', 'Sample\SampleReturnController@create');
        Route::post('confirm', 'Sample\SampleReturnController@confirm');
        Route::get('export', 'Sample\SampleReturnController@export');
    });
});
```

**路由设计要点：**

- 按功能模块进行路由分组，使用有意义的前缀
- 路由命名遵循标准：list、details、create、update、delete、export、import
- 除导出使用GET外，其余统一使用POST方法
- 明确中间件配置（auth、signature等）
- 路由结构清晰，层次分明

## 使用这些示例

在编写技术设计方案时：

1. **参考格式**：严格按照这些示例的格式编写各层次的设计
2. **完整注释**：确保所有PHPDoc注释都完整详细
3. **逻辑清晰**：服务层的逻辑注释要详细到可以直接转化为代码
4. **类型明确**：所有参数和返回值都要明确类型
5. **规范一致**：保持命名规范、代码风格的一致性

这些示例展示了如何编写高质量的设计文档，确保代码实现阶段可以严格按照设计执行。
