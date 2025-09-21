# 活动和积分系统API文档

## 概述

本文档详细说明了活动和积分系统的所有API接口，包括活动管理、积分管理、积分商城、活动签到、活动评价和积分统计等功能。

## 模块结构

系统包含三个主要服务模块：

1. **活动管理服务模块** (activity-service.ts) - 负责活动创建、管理和报名功能
2. **积分管理服务模块** (point-service.ts) - 负责积分记录、余额查询和转账功能
3. **积分商城和活动增强服务模块** (point-store-service.ts) - 负责积分商城、活动签到、评价和统计功能

## 通用响应格式

所有API接口使用统一的响应格式：

```json
{
  "code": 0,        // 0表示成功，非0表示错误
  "message": "",    // 响应消息
  "data": {}        // 响应数据
}
```

## 1. 活动管理服务模块 (activity-service.ts)

### 1.1 活动分类接口

#### 获取活动分类列表
- **URL**: `GET /api/v1/activities/categories`
- **描述**: 获取系统预定义的活动分类列表
- **权限**: 无需特殊权限
- **请求参数**: 无
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": [
    { "id": 1, "name": "学术讲座" },
    { "id": 2, "name": "技术工作坊" },
    { "id": 3, "name": "社团活动" },
    { "id": 4, "name": "体育竞赛" },
    { "id": 5, "name": "文艺表演" },
    { "id": 6, "name": "志愿服务" },
    { "id": 7, "name": "其他" }
  ]
}
```

### 1.2 活动管理接口

#### 获取活动列表
- **URL**: `GET /api/v1/activities`
- **描述**: 获取活动列表，支持分页、状态筛选和搜索
- **权限**: 无需特殊权限
- **请求参数**:
  - `page` (可选): 页码，默认为1
  - `pageSize` (可选): 每页数量，默认为10
  - `status` (可选): 活动状态筛选 (upcoming, ongoing, ended, completed, cancelled)
  - `search` (可选): 搜索关键词，搜索标题和描述
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "title": "前端技术分享会",
        "description": "分享最新的前端技术趋势",
        "location": "会议室A",
        "startTime": "2023-06-15T14:00:00Z",
        "endTime": "2023-06-15T16:00:00Z",
        "signupDeadline": "2023-06-14T18:00:00Z",
        "maxParticipants": 50,
        "currentParticipants": 25,
        "pointsAwarded": 10,
        "status": "upcoming",
        "createdById": 1,
        "createdBy": {
          "id": 1,
          "username": "admin"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

#### 创建活动
- **URL**: `POST /api/v1/activities`
- **描述**: 创建新活动（仅管理员）
- **权限**: 管理员
- **请求参数**:
```json
{
  "title": "前端技术分享会",
  "description": "分享最新的前端技术趋势",
  "location": "会议室A",
  "startTime": "2023-06-15T14:00:00Z",
  "endTime": "2023-06-15T16:00:00Z",
  "signupDeadline": "2023-06-14T18:00:00Z",
  "maxParticipants": 50,
  "pointsAwarded": 10
}
```
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "id": 1,
    "title": "前端技术分享会",
    "description": "分享最新的前端技术趋势",
    "location": "会议室A",
    "startTime": "2023-06-15T14:00:00Z",
    "endTime": "2023-06-15T16:00:00Z",
    "signupDeadline": "2023-06-14T18:00:00Z",
    "maxParticipants": 50,
    "currentParticipants": 0,
    "pointsAwarded": 10,
    "status": "upcoming",
    "createdById": 1
  }
}
```

#### 获取活动详情
- **URL**: `GET /api/v1/activities/:id`
- **描述**: 获取指定活动的详细信息
- **权限**: 无需特殊权限
- **请求参数**: 路径参数 `id` - 活动ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "id": 1,
    "title": "前端技术分享会",
    "description": "分享最新的前端技术趋势",
    "location": "会议室A",
    "startTime": "2023-06-15T14:00:00Z",
    "endTime": "2023-06-15T16:00:00Z",
    "signupDeadline": "2023-06-14T18:00:00Z",
    "maxParticipants": 50,
    "currentParticipants": 25,
    "pointsAwarded": 10,
    "status": "upcoming",
    "createdById": 1,
    "createdBy": {
      "id": 1,
      "username": "admin"
    },
    "registrations": [
      {
        "id": 1,
        "userId": 2,
        "activityId": 1,
        "registrationTime": "2023-06-10T10:00:00Z",
        "status": "approved",
        "pointsAwarded": 0
      }
    ]
  }
}
```

#### 更新活动
- **URL**: `PUT /api/v1/activities/:id`
- **描述**: 更新活动信息（仅管理员）
- **权限**: 管理员
- **请求参数**: 路径参数 `id` - 活动ID
```json
{
  "title": "前端技术分享会（更新）",
  "description": "分享最新的前端技术趋势和最佳实践",
  "location": "会议室B",
  "startTime": "2023-06-15T14:00:00Z",
  "endTime": "2023-06-15T17:00:00Z",
  "signupDeadline": "2023-06-14T18:00:00Z",
  "maxParticipants": 60,
  "pointsAwarded": 15,
  "status": "upcoming"
}
```
- **响应示例**: 同创建活动响应

#### 删除活动
- **URL**: `DELETE /api/v1/activities/:id`
- **描述**: 删除活动（仅管理员）
- **权限**: 管理员
- **请求参数**: 路径参数 `id` - 活动ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": null
}
```

### 1.3 活动报名接口

#### 报名活动
- **URL**: `POST /api/v1/activities/:id/register`
- **描述**: 用户报名参加活动
- **权限**: 已登录用户
- **请求参数**: 路径参数 `id` - 活动ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "报名成功，等待审核",
  "data": {
    "registrationId": 1,
    "status": "pending"
  }
}
```

#### 取消报名
- **URL**: `DELETE /api/v1/activities/:id/register`
- **描述**: 用户取消活动报名
- **权限**: 已登录用户
- **请求参数**: 路径参数 `id` - 活动ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "取消报名成功",
  "data": null
}
```

#### 获取活动报名列表
- **URL**: `GET /api/v1/activities/:id/registrations`
- **描述**: 获取指定活动的报名列表（仅管理员）
- **权限**: 管理员
- **请求参数**: 
  - 路径参数 `id` - 活动ID
  - 查询参数 `page` (可选): 页码，默认为1
  - 查询参数 `pageSize` (可选): 每页数量，默认为10
  - 查询参数 `status` (可选): 报名状态筛选 (pending, approved, rejected, cancelled)
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "userId": 2,
        "activityId": 1,
        "registrationTime": "2023-06-10T10:00:00Z",
        "status": "approved",
        "pointsAwarded": 0,
        "user": {
          "id": 2,
          "username": "user1"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

#### 批准报名
- **URL**: `PUT /api/v1/activities/:id/registrations/:registrationId/approve`
- **描述**: 批准活动报名（仅管理员）
- **权限**: 管理员
- **请求参数**: 
  - 路径参数 `id` - 活动ID
  - 路径参数 `registrationId` - 报名记录ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "批准报名成功",
  "data": null
}
```

#### 拒绝报名
- **URL**: `PUT /api/v1/activities/:id/registrations/:registrationId/reject`
- **描述**: 拒绝活动报名（仅管理员）
- **权限**: 管理员
- **请求参数**: 
  - 路径参数 `id` - 活动ID
  - 路径参数 `registrationId` - 报名记录ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "拒绝报名成功",
  "data": null
}
```

### 1.4 用户活动记录接口

#### 获取当前用户活动报名记录
- **URL**: `GET /api/v1/users/me/activities`
- **描述**: 获取当前用户的活动报名记录
- **权限**: 已登录用户
- **请求参数**:
  - `page` (可选): 页码，默认为1
  - `pageSize` (可选): 每页数量，默认为10
  - `status` (可选): 报名状态筛选 (pending, approved, rejected, cancelled)
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "userId": 2,
        "activityId": 1,
        "registrationTime": "2023-06-10T10:00:00Z",
        "status": "approved",
        "pointsAwarded": 0,
        "activity": {
          "id": 1,
          "title": "前端技术分享会",
          "startTime": "2023-06-15T14:00:00Z",
          "endTime": "2023-06-15T16:00:00Z",
          "location": "会议室A",
          "pointsAwarded": 10,
          "status": "upcoming"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

#### 活动结束后自动发放积分
- **URL**: `POST /api/v1/activities/:id/complete`
- **描述**: 活动结束后自动为参与用户发放积分（仅管理员）
- **权限**: 管理员
- **请求参数**: 路径参数 `id` - 活动ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "积分发放成功",
  "data": {
    "totalParticipants": 25,
    "pointsPerParticipant": 10,
    "totalPointsAwarded": 250
  }
}
```

## 2. 积分管理服务模块 (point-service.ts)

### 2.1 积分记录管理接口

#### 创建积分记录
- **URL**: `POST /api/v1/points`
- **描述**: 创建积分记录（仅管理员）
- **权限**: 管理员
- **请求参数**:
```json
{
  "userId": 2,
  "points": 10,
  "type": "earned",
  "description": "活动参与奖励",
  "relatedEntityType": "activity",
  "relatedEntityId": 1
}
```
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "id": 1,
    "userId": 2,
    "points": 10,
    "type": "earned",
    "description": "活动参与奖励",
    "relatedEntityType": "activity",
    "relatedEntityId": 1,
    "createdAt": "2023-06-10T10:00:00Z"
  }
}
```

#### 获取用户积分余额
- **URL**: `GET /api/v1/users/:userId/points/balance`
- **描述**: 获取指定用户的积分余额
- **权限**: 管理员或用户本人
- **请求参数**: 路径参数 `userId` - 用户ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "userId": 2,
    "balance": 150
  }
}
```

#### 获取当前用户积分余额
- **URL**: `GET /api/v1/users/me/points/balance`
- **描述**: 获取当前用户的积分余额
- **权限**: 已登录用户
- **请求参数**: 无
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "userId": 2,
    "balance": 150
  }
}
```

#### 管理员调整积分
- **URL**: `POST /api/v1/users/:userId/points/adjust`
- **描述**: 管理员调整用户积分（仅管理员）
- **权限**: 管理员
- **请求参数**: 
  - 路径参数 `userId` - 用户ID
```json
{
  "points": 20,
  "reason": "活动奖励补充"
}
```
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "id": 2,
    "userId": 2,
    "points": 20,
    "type": "admin_adjustment",
    "description": "活动奖励补充",
    "createdAt": "2023-06-10T11:00:00Z"
  }
}
```

#### 获取用户积分记录列表
- **URL**: `GET /api/v1/users/:userId/points/records`
- **描述**: 获取指定用户的积分记录列表
- **权限**: 管理员或用户本人
- **请求参数**: 
  - 路径参数 `userId` - 用户ID
  - 查询参数 `page` (可选): 页码，默认为1
  - 查询参数 `pageSize` (可选): 每页数量，默认为10
  - 查询参数 `type` (可选): 积分类型筛选 (earned, spent, admin_adjustment, transfer)
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "userId": 2,
        "points": 10,
        "type": "earned",
        "description": "活动参与奖励",
        "relatedEntityType": "activity",
        "relatedEntityId": 1,
        "createdAt": "2023-06-10T10:00:00Z"
      },
      {
        "id": 2,
        "userId": 2,
        "points": -5,
        "type": "spent",
        "description": "兑换商品",
        "relatedEntityType": "product",
        "relatedEntityId": 1,
        "createdAt": "2023-06-10T11:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 2,
      "totalPages": 1
    }
  }
}
```

#### 获取当前用户积分记录列表
- **URL**: `GET /api/v1/users/me/points/records`
- **描述**: 获取当前用户的积分记录列表
- **权限**: 已登录用户
- **请求参数**: 
  - 查询参数 `page` (可选): 页码，默认为1
  - 查询参数 `pageSize` (可选): 每页数量，默认为10
  - 查询参数 `type` (可选): 积分类型筛选 (earned, spent, admin_adjustment, transfer)
- **响应示例**: 同获取用户积分记录列表

### 2.2 活动积分接口

#### 获取用户积分活动记录
- **URL**: `GET /api/v1/users/:userId/points/activity-records`
- **描述**: 获取指定用户的活动积分记录
- **权限**: 管理员或用户本人
- **请求参数**: 
  - 路径参数 `userId` - 用户ID
  - 查询参数 `page` (可选): 页码，默认为1
  - 查询参数 `pageSize` (可选): 每页数量，默认为10
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "userId": 2,
        "points": 10,
        "type": "earned",
        "description": "活动参与奖励",
        "relatedEntityType": "activity",
        "relatedEntityId": 1,
        "createdAt": "2023-06-10T10:00:00Z",
        "activity": {
          "id": 1,
          "title": "前端技术分享会",
          "startTime": "2023-06-15T14:00:00Z",
          "endTime": "2023-06-15T16:00:00Z"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

#### 获取当前用户积分活动记录
- **URL**: `GET /api/v1/users/me/points/activity-records`
- **描述**: 获取当前用户的活动积分记录
- **权限**: 已登录用户
- **请求参数**: 
  - 查询参数 `page` (可选): 页码，默认为1
  - 查询参数 `pageSize` (可选): 每页数量，默认为10
- **响应示例**: 同获取用户积分活动记录

#### 获取活动积分统计信息
- **URL**: `GET /api/v1/statistics/activities/points`
- **描述**: 获取活动积分统计信息（仅管理员）
- **权限**: 管理员
- **请求参数**: 
  - 查询参数 `timeRange` (可选): 时间范围 (day, week, month, year)，默认为month
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "timeRange": "month",
    "totalActivities": 5,
    "totalPointsAwarded": 500,
    "averagePointsPerActivity": 100,
    "topActivities": [
      {
        "id": 1,
        "title": "前端技术分享会",
        "pointsAwarded": 200,
        "participantCount": 20
      }
    ]
  }
}
```

### 2.3 积分转账接口

#### 积分转账
- **URL**: `POST /api/v1/points/transfer`
- **描述**: 用户积分转账
- **权限**: 已登录用户
- **请求参数**:
```json
{
  "targetUserId": 3,
  "points": 10,
  "message": "请喝咖啡"
}
```
- **响应示例**:
```json
{
  "code": 0,
  "message": "转账成功",
  "data": {
    "transferId": 1,
    "points": 10,
    "message": "请喝咖啡"
  }
}
```

#### 获取积分排行榜
- **URL**: `GET /api/v1/points/leaderboard`
- **描述**: 获取积分排行榜
- **权限**: 无需特殊权限
- **请求参数**: 
  - 查询参数 `limit` (可选): 返回记录数量，默认为10
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "leaderboard": [
      {
        "userId": 2,
        "username": "user1",
        "balance": 150,
        "rank": 1
      },
      {
        "userId": 3,
        "username": "user2",
        "balance": 120,
        "rank": 2
      }
    ]
  }
}
```

## 3. 积分商城和活动增强服务模块 (point-store-service.ts)

### 3.1 积分商城接口

#### 获取积分商品列表
- **URL**: `GET /api/v1/point-products`
- **描述**: 获取积分商品列表
- **权限**: 无需特殊权限
- **请求参数**:
  - `page` (可选): 页码，默认为1
  - `pageSize` (可选): 每页数量，默认为10
  - `type` (可选): 商品类型筛选 (physical, digital, coupon)
  - `status` (可选): 商品状态筛选 (available, out_of_stock, discontinued)，默认为available
  - `search` (可选): 搜索关键词，搜索名称和描述
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "name": "精美笔记本",
        "description": "高质量笔记本，适合记录学习笔记",
        "points": 50,
        "stock": 20,
        "soldCount": 5,
        "imageUrl": "https://example.com/notebook.jpg",
        "status": "available",
        "type": "physical",
        "metadata": null,
        "createdAt": "2023-06-01T10:00:00Z",
        "updatedAt": "2023-06-01T10:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

#### 获取积分商品详情
- **URL**: `GET /api/v1/point-products/:id`
- **描述**: 获取积分商品详情
- **权限**: 无需特殊权限
- **请求参数**: 路径参数 `id` - 商品ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "id": 1,
    "name": "精美笔记本",
    "description": "高质量笔记本，适合记录学习笔记",
    "points": 50,
    "stock": 20,
    "soldCount": 5,
    "imageUrl": "https://example.com/notebook.jpg",
    "status": "available",
    "type": "physical",
    "metadata": null,
    "createdAt": "2023-06-01T10:00:00Z",
    "updatedAt": "2023-06-01T10:00:00Z"
  }
}
```

#### 创建积分商品
- **URL**: `POST /api/v1/point-products`
- **描述**: 创建积分商品（仅管理员）
- **权限**: 管理员
- **请求参数**:
```json
{
  "name": "精美笔记本",
  "description": "高质量笔记本，适合记录学习笔记",
  "points": 50,
  "stock": 20,
  "imageUrl": "https://example.com/notebook.jpg",
  "type": "physical",
  "metadata": null
}
```
- **响应示例**: 同获取积分商品详情

#### 更新积分商品
- **URL**: `PUT /api/v1/point-products/:id`
- **描述**: 更新积分商品（仅管理员）
- **权限**: 管理员
- **请求参数**: 
  - 路径参数 `id` - 商品ID
```json
{
  "name": "高级笔记本",
  "description": "高质量笔记本，适合记录学习笔记",
  "points": 60,
  "stock": 15,
  "imageUrl": "https://example.com/notebook.jpg",
  "type": "physical",
  "status": "available",
  "metadata": null
}
```
- **响应示例**: 同获取积分商品详情

#### 删除积分商品
- **URL**: `DELETE /api/v1/point-products/:id`
- **描述**: 删除积分商品（仅管理员）
- **权限**: 管理员
- **请求参数**: 路径参数 `id` - 商品ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": null
}
```

#### 兑换积分商品
- **URL**: `POST /api/v1/point-products/:id/exchange`
- **描述**: 兑换积分商品
- **权限**: 已登录用户
- **请求参数**: 
  - 路径参数 `id` - 商品ID
```json
{
  "quantity": 1,
  "notes": "请送到我的办公室"
}
```
- **响应示例**:
```json
{
  "code": 0,
  "message": "兑换成功",
  "data": {
    "exchangeId": 1,
    "message": "兑换成功",
    "points": 50,
    "quantity": 1
  }
}
```

#### 获取用户兑换记录列表
- **URL**: `GET /api/v1/users/me/point-exchanges`
- **描述**: 获取当前用户的兑换记录列表
- **权限**: 已登录用户
- **请求参数**:
  - `page` (可选): 页码，默认为1
  - `pageSize` (可选): 每页数量，默认为10
  - `status` (可选): 兑换状态筛选 (pending, completed, cancelled)
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "userId": 2,
        "productId": 1,
        "points": 50,
        "quantity": 1,
        "status": "pending",
        "notes": "请送到我的办公室",
        "exchangeData": null,
        "createdAt": "2023-06-10T12:00:00Z",
        "updatedAt": "2023-06-10T12:00:00Z",
        "product": {
          "id": 1,
          "name": "精美笔记本",
          "description": "高质量笔记本，适合记录学习笔记",
          "imageUrl": "https://example.com/notebook.jpg"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

#### 获取用户兑换记录详情
- **URL**: `GET /api/v1/users/me/point-exchanges/:id`
- **描述**: 获取当前用户的兑换记录详情
- **权限**: 已登录用户
- **请求参数**: 路径参数 `id` - 兑换记录ID
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "id": 1,
    "userId": 2,
    "productId": 1,
    "points": 50,
    "quantity": 1,
    "status": "pending",
    "notes": "请送到我的办公室",
    "exchangeData": null,
    "createdAt": "2023-06-10T12:00:00Z",
    "updatedAt": "2023-06-10T12:00:00Z",
    "product": {
      "id": 1,
      "name": "精美笔记本",
      "description": "高质量笔记本，适合记录学习笔记",
      "imageUrl": "https://example.com/notebook.jpg"
    }
  }
}
```

### 3.2 活动签到接口

#### 活动签到
- **URL**: `POST /api/v1/activities/:id/checkin`
- **描述**: 活动签到
- **权限**: 已登录用户
- **请求参数**: 
  - 路径参数 `id` - 活动ID
```json
{
  "notes": "提前到达，准备充分"
}
```
- **响应示例**:
```json
{
  "code": 0,
  "message": "签到成功",
  "data": {
    "checkInId": 1,
    "status": "on_time",
    "pointsAwarded": 2,
    "message": "签到成功"
  }
}
```

#### 获取活动签到记录
- **URL**: `GET /api/v1/activities/:id/checkins`
- **描述**: 获取活动签到记录（仅管理员）
- **权限**: 管理员
- **请求参数**: 
  - 路径参数 `id` - 活动ID
  - 查询参数 `page` (可选): 页码，默认为1
  - 查询参数 `pageSize` (可选): 每页数量，默认为10
  - 查询参数 `status` (可选): 签到状态筛选 (on_time, late, early_leave)
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "userId": 2,
        "activityId": 1,
        "checkInTime": "2023-06-15T13:55:00Z",
        "status": "on_time",
        "notes": "提前到达，准备充分",
        "pointsAwarded": 2,
        "createdAt": "2023-06-15T13:55:00Z",
        "updatedAt": "2023-06-15T13:55:00Z",
        "user": {
          "id": 2,
          "username": "user1"
        },
        "activity": {
          "id": 1,
          "title": "前端技术分享会",
          "startTime": "2023-06-15T14:00:00Z",
          "endTime": "2023-06-15T16:00:00Z"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

#### 获取用户签到记录
- **URL**: `GET /api/v1/users/me/activity-checkins`
- **描述**: 获取当前用户的签到记录
- **权限**: 已登录用户
- **请求参数**:
  - `page` (可选): 页码，默认为1
  - `pageSize` (可选): 每页数量，默认为10
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "userId": 2,
        "activityId": 1,
        "checkInTime": "2023-06-15T13:55:00Z",
        "status": "on_time",
        "notes": "提前到达，准备充分",
        "pointsAwarded": 2,
        "createdAt": "2023-06-15T13:55:00Z",
        "updatedAt": "2023-06-15T13:55:00Z",
        "activity": {
          "id": 1,
          "title": "前端技术分享会",
          "startTime": "2023-06-15T14:00:00Z",
          "endTime": "2023-06-15T16:00:00Z"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

### 3.3 活动评价接口

#### 活动评价
- **URL**: `POST /api/v1/activities/:id/reviews`
- **描述**: 提交活动评价
- **权限**: 已登录用户
- **请求参数**: 
  - 路径参数 `id` - 活动ID
```json
{
  "rating": 5,
  "content": "活动内容丰富，组织有序，收获很大！",
  "isAnonymous": false
}
```
- **响应示例**:
```json
{
  "code": 0,
  "message": "评价成功",
  "data": {
    "reviewId": 1,
    "pointsAwarded": 5,
    "message": "评价成功"
  }
}
```

#### 获取活动评价列表
- **URL**: `GET /api/v1/activities/:id/reviews`
- **描述**: 获取活动评价列表
- **权限**: 无需特殊权限
- **请求参数**: 
  - 路径参数 `id` - 活动ID
  - 查询参数 `page` (可选): 页码，默认为1
  - 查询参数 `pageSize` (可选): 每页数量，默认为10
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "userId": 2,
        "activityId": 1,
        "rating": 5,
        "content": "活动内容丰富，组织有序，收获很大！",
        "isAnonymous": false,
        "createdAt": "2023-06-16T10:00:00Z",
        "updatedAt": "2023-06-16T10:00:00Z",
        "user": {
          "id": 2,
          "username": "user1"
        },
        "activity": {
          "id": 1,
          "title": "前端技术分享会"
        }
      }
    ],
    "averageRating": "5.00",
    "totalReviews": 1,
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

#### 获取用户评价记录
- **URL**: `GET /api/v1/users/me/activity-reviews`
- **描述**: 获取当前用户的评价记录
- **权限**: 已登录用户
- **请求参数**:
  - `page` (可选): 页码，默认为1
  - `pageSize` (可选): 每页数量，默认为10
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "list": [
      {
        "id": 1,
        "userId": 2,
        "activityId": 1,
        "rating": 5,
        "content": "活动内容丰富，组织有序，收获很大！",
        "isAnonymous": false,
        "createdAt": "2023-06-16T10:00:00Z",
        "updatedAt": "2023-06-16T10:00:00Z",
        "activity": {
          "id": 1,
          "title": "前端技术分享会",
          "startTime": "2023-06-15T14:00:00Z",
          "endTime": "2023-06-15T16:00:00Z"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 1,
      "totalPages": 1
    }
  }
}
```

### 3.4 积分统计报表接口（管理员）

#### 获取积分统计报表
- **URL**: `GET /api/v1/statistics/points`
- **描述**: 获取积分统计报表（仅管理员）
- **权限**: 管理员
- **请求参数**:
  - 查询参数 `timeRange` (可选): 时间范围 (day, week, month, year)，默认为month
- **响应示例**:
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "timeRange": "month",
    "earnedStats": {
      "totalEarned": 1000,
      "totalEarnedRecords": 50
    },
    "spentStats": {
      "totalSpent": 300,
      "totalSpentRecords": 15
    },
    "transferStats": {
      "totalTransferred": 200,
      "totalTransferRecords": 10
    },
    "activityStats": {
      "totalActivityPoints": 500,
      "totalActivities": 5
    },
    "productStats": {
      "totalProductPoints": 300,
      "totalExchanges": 15,
      "totalProducts": 10
    },
    "leaderboard": [
      {
        "userId": 2,
        "username": "user1",
        "balance": 150
      },
      {
        "userId": 3,
        "username": "user2",
        "balance": 120
      }
    ]
  }
}
```

## 错误码说明

| 错误码 | 说明 |
|--------|------|
| 0 | 成功 |
| 401 | 用户未登录或登录已过期 |
| 403 | 无权限访问 |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |

## 注意事项

1. 所有需要用户身份验证的接口都需要在请求头中包含有效的认证信息（如JWT token）
2. 管理员权限的接口只能由角色为"admin"的用户访问
3. 分页参数中，page从1开始计数
4. 时间格式统一使用ISO 8601格式（如：2023-06-15T14:00:00Z）
5. 所有金额和积分相关字段都使用整数类型，单位为分或积分