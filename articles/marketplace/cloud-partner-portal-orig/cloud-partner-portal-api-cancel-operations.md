---
title: 取消操作 API |Azure 应用商店
description: 取消操作。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256427"
---
# <a name="cancel-operation"></a>取消操作

> [!NOTE]
> 云合作伙伴门户 API 与合作伙伴中心集成，在您的产品/服务迁移到合作伙伴中心后将继续工作。 集成引入了小更改。 查看[云合作伙伴门户 API 参考](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)中列出的更改，以确保代码在迁移到合作伙伴中心后继续工作。

此 API 会取消当前正在对产品/服务进行的操作。 使用[检索操作 API](./cloud-partner-portal-api-retrieve-operations.md) 获取要传递给此 API 的 `operationId`。 取消通常是同步操作，但是在某些复杂情况下，可能需要执行新操作来取消现有操作。 在这种情况下，HTTP 响应正文包含应该用于查询状态的操作位置。

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 参数

--------------

|  **名称**    |      **说明**                                  |    **数据类型**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  发布者标识符，例如 `contoso`         |   字符串          |
| offerId      |  产品/服务标识符                                     |   字符串          |
| api-version  |  API 的当前版本                               |    Date           |
|  |  |  |

## <a name="header"></a>标头
------

|  **名称**              |  **价值**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  授权         |  持有者令牌 |
|  |  |

## <a name="body-example"></a>正文示例
------------

### <a name="request"></a>请求

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>请求正文属性

|  **名称**                |  **说明**                                               |
|  --------                |  ---------------                                               |
|  notification-emails     | 逗号分隔的电子邮件 ID 列表，用于通知发布操作的进度。 |
|  |  |

### <a name="response"></a>响应

#### <a name="migrated-offers"></a>迁移优惠

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>非迁移优惠

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>响应标头

|  **名称**             |    **价值**                       |
|  ---------            |    ----------                      |
| 位置    | 检索此操作状态的相对路径。 |
|  |  |

### <a name="response-status-codes"></a>响应状态代码

| **代码**  |  **说明**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | 正常。 请求已成功处理，并且已同步取消操作。 |
|  202      | 已接受。 请求已成功处理，并且正在取消操作。 取消操作的位置将在响应标头中返回。 |
|  400      | 错误/格式错误请求。 错误响应正文可以提供更多信息。  |
|  403      | 访问被禁止。 客户端无权访问请求中指定的命名空间。 |
|  404      | 未找到。 指定的实体不存在。 |
|  |  |
