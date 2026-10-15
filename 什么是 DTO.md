# 什么是 DTO

数据库存储entity以及schema  <——> API 接口I/O <——>前后端业务逻辑

DTO位于 数据库与 API之间，因为我们使用API接口传递数据时，很多场景下，并不需要传递一个entity的所有数据，因此使用DTO来定义不同情况下传递该entity的哪些数据，比如store这个entity的数据使用API传递给店铺的后台管理系统和店铺的面向客人的点餐网站一定是不一样的，店铺后台系统需要一些隐私，比如手续费率，隐藏菜单，促销设计等等，给点餐网站时，API需要传菜单，地址之类的



> **DTO 位于“业务内部模型（Entity / Domain）与 API 接口之间”**

## 一、DTO 是什么？

**DTO（Data Transfer Object）= 数据传输对象**

- 用来在系统边界之间传递数据的对象
- 只负责 数据结构，不负责业务逻辑

常见使用场景：

- 后端 → 前端（API Response）
- 前端 → 后端（API Request）
- 服务 ↔ 服务（微服务 / 模块调用）

## 二、DTO 不是什么？

DTO ❌ **不是**：

- 数据库实体（Entity）
- 业务对象（Domain Model）
- 工具类 / Service

DTO 的原则：稳定、干净、可控

## 三、为什么要用 DTO？（核心动机）

### 1️⃣ 防止“无意中泄露数据”

- Entity 是给数据库用的，不是给前端看的

如果直接返回 Entity：

- 新增字段
- 内部字段
- 敏感字段

👉 **可能在你“什么都没改”的情况下，直接暴露给前端**

DTO 的作用：

- 锁死接口返回结构

### 2️⃣ 解耦前端与数据库结构

数据库常见写法：

```
status: Int  // 0,1,2
```

前端希望：

```
"status": "ACTIVE"
```

DTO 可以：

- 转换数据格式
- 提供语义清晰的字段
- 屏蔽数据库实现细节

### **3️⃣ 支持“同一数据，不同视图”**

同一个 Entity：

- 管理后台
- 商家端
- 用户端

返回内容 **一定不同**

DTO 允许：

```
StoreAdminDTO
StorePublicDTO
StoreSimpleDTO
```

👉 避免 Entity 被 API 逻辑污染

### **4️⃣ 控制系统边界（API Contract）**

DTO = 后端对外的 **数据契约（Contract）**

好处：

- 后端可以随意重构 Entity
- 不影响前端
- 不影响已有 API 使用方

## **四、DTO vs Entity vs VO（必会对比）**



| **类型**   | **用途**   | **面向谁**     |
| ---------- | ---------- | -------------- |
| **DTO**    | 数据传输   | API / 外部系统 |
| **Entity** | 数据持久化 | 数据库         |
| **VO**     | 业务值对象 | 领域逻辑       |

一句话记忆：

> **Entity 给数据库看，DTO 给接口看，VO 给业务看**

## **五、DTO 的常见分类（实战）**

### **1️⃣ Request DTO（入参）**

```
data class LoginRequestDTO(
  val username: String,
  val password: String,
  val principle: String
)
```

特点：

- 用于接收前端请求
- 常配合校验注解

### **2️⃣ Response DTO（出参）**

```
data class LoginResponseDTO(
  val accessToken: String,
  val expiresIn: Long
)
```

特点：

- 只返回前端需要的字段
- 结构稳定

### **3️⃣ 内部 DTO（模块 / 服务间）**

```
data class StoreProfileDTO(
  val id: Long,
  val config: StoreConfig
)
```

## **六、DTO 的设计原则（重要）**

### ✅ 推荐

- 使用 data class
- 字段含义清晰
- 字段名偏业务语义
- 明确 Request / Response 角色

### **❌ 不推荐**

- 在 DTO 里写业务逻辑
- 直接暴露 Entity
- DTO 和 Entity 一模一样（那 DTO 就失去意义）

## **七、为什么“刚开始看不出 DTO 的区别”？**

> **DTO 的价值不在“这一次返回”，而在“以后敢不敢改代码”**

- 项目小：感觉不到
- 单人开发：感觉不到
- 没上线：感觉不到

一旦：

- 多人协作
- 接口被外部使用
- 需求开始变

👉 **不用 DTO 的成本会指数级上升**



















# **一、什么是「Repository 查询」？**

### **一句话定义**

> **Repository 查询 = 后端在代码里，从数据库“拿数据”的那一步**

它回答的是：

> **我要从数据库拿什么数据？**

## **1️⃣ Repository 是什么？**

在典型后端结构里：

```
Controller  →  Service  →  Repository  →  Database
```

**Repository**：

- 专门负责 **访问数据库**
- 不关心 HTTP、JSON、前端
- 返回的是 **Entity 或数据库投影**

## **2️⃣ Repository 查询具体指什么？**

比如：

```
storeRepository.findById(id)
storeRepository.findAllByStatus(ACTIVE)
```

这些就是 **Repository 查询**。

本质上：

- SQL / ORM 查询
- 面向 **数据库结构**
- 返回 **Entity / Entity 列表**

## **3️⃣ Repository 查询关注什么？**

| **关注点** | **说明**           |
| ---------- | ------------------ |
| 表结构     | 字段、关联         |
| 性能       | join / 索引        |
| 正确性     | 查到的数据是否完整 |

👉 **它不关心：**

- 返回给谁
- 返回成什么 JSON
- 是否要隐藏字段

# **二、什么是「API 出参」？**

### **一句话定义**

> **API 出参 = 通过接口返回给“接口调用方”的数据**

它回答的是：

> **我允许外部系统看到什么数据？**

## **1️⃣ API 出参在代码里是哪一层？**

```
Controller  ←  API 出参
```

示例：

```
@GetMapping("/store/profile")
fun getProfile(): StoreProfileDTO {
    return storeProfileDTO
}
```

返回的这个 StoreProfileDTO，就是 **API 出参**。

## **2️⃣ API 出参关注什么？**

| **关注点** | **说明**           |
| ---------- | ------------------ |
| 安全性     | 是否泄露敏感字段   |
| 稳定性     | 字段是否可长期使用 |
| 语义       | 前端是否容易理解   |

👉 **它不关心：**

- 数据是怎么查出来的
- 表结构长什么样
- 是否 join 了几张表

# **三、两者最容易混淆的点（重点）**

### **❌ 错误理解**

> “Repository 查询的结果 = API 出参”

这是**新手最容易犯的错**。

### **✅ 正确关系（非常重要）**

```
Repository 查询（Entity / 原始数据）
            ↓
        业务加工
            ↓
      DTO 组装 / 映射
            ↓
        API 出参（DTO）
```

👉 **中间那一步不能省**

# **四、用 Store 的真实例子对照**

### **Repository 查询**



```
val store = storeRepository.findById(id)
```

拿到的是：

```
StoreEntity(
  id = 1,
  bankAccount = "...",
  feeRate = 0.03,
  internalStatus = 2
)
```

### **API 出参**

```
StorePublicDTO(
  id = store.id,
  name = store.name,
  address = store.address
)
```

返回给前端：

```
{
  "id": 1,
  "name": "Sydney Cafe",
  "address": "..."
}
```

## **⚠️ 如果你直接把 Repository 查询结果返回？**

```
@GetMapping("/store/profile")
fun getProfile(): StoreEntity {
  return store
}
```

🚨 后果：

- 新字段会自动暴露
- 安全事故
- 接口不稳定

# **五、一句话对比总结（很适合记笔记）**

> **Repository 查询关心“数据库里有什么”**

> **API 出参关心“我允许你看到什么”**

