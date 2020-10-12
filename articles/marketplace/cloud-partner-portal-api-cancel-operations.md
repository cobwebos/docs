---
title: 取消操作 API-Microsoft 商用 marketplace
description: 用于取消产品/服务上当前正在进行的操作的 API
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 095d9cf68374ee81cafdb6e7a32df29a57d122cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287928"
---
# <a name="cancel-operation"></a>取消操作

> [!NOTE]
> 云合作伙伴门户 Api 与集成，并将在合作伙伴中心继续工作。 转换引入了少量更改。 查看 [云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md) 中列出的更改，确保你的代码在转换到合作伙伴中心后继续工作。 CPP Api 仅适用于过渡到合作伙伴中心之前已集成的现有产品;新产品应使用合作伙伴中心提交 Api。

此 API 会取消当前正在对产品/服务进行的操作。 使用[检索操作 API](./cloud-partner-portal-api-retrieve-operations.md) 获取要传递给此 API 的 `operationId`。 取消通常是同步操作，但是在某些复杂情况下，可能需要执行新操作来取消现有操作。 在这种情况下，HTTP 响应正文包含应该用于查询状态的操作位置。

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 参数

--------------

|  **名称**    |      **描述**                                  |    **Data type**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  发布者标识符，例如 `contoso`         |   字符串          |
| offerId      |  产品/服务标识符                                     |   字符串          |
| api-version  |  API 的当前版本                               |    Date           |
|  |  |  |

## <a name="header"></a>Header
------

|  **Name**              |  **值**         |
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

|  **名称**                |  **描述**                                               |
|  --------                |  ---------------                                               |
|  notification-emails     | 逗号分隔的电子邮件 ID 列表，用于通知发布操作的进度。 |
|  |  |

### <a name="response"></a>响应

#### <a name="migrated-offers"></a>已迁移的产品/服务

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>未迁移的产品/服务

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>响应标头

|  **Name**             |    **值**                       |
|  ---------            |    ----------                      |
| 位置    | 用于检索此操作的状态的相对路径。 |
|  |  |

### <a name="response-status-codes"></a>响应状态代码

| **代码**  |  **描述**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | 正常。 请求已成功处理，并且已同步取消操作。 |
|  202      | 已接受。 请求已成功处理，并且正在取消操作。 取消操作的位置将在响应标头中返回。 |
|  400      | 错误/格式错误请求。 错误响应正文可以提供更多信息。  |
|  403      | 访问被禁止。 客户端无权访问请求中指定的命名空间。 |
|  404      | 未找到。 指定的实体不存在。 |
|  |  |
