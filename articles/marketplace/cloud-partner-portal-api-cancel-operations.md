---
title: 取消操作 API-Microsoft 商用 marketplace
description: 用于取消产品/服务上当前正在进行的操作的 API
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/16/2020
ms.author: mingshen
ms.openlocfilehash: 99a6cebd22c88388c68ceff3873ea8f8782b1c87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515865"
---
# <a name="cancel-operation"></a>取消操作

> [!NOTE]
> 云合作伙伴门户 API 已与合作伙伴中心集成，在你的产品/服务迁移到合作伙伴中心后，它们仍可运行。 集成造成了少量的更改。 查看[云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md)中列出的更改，以确保你的代码在迁移到合作伙伴中心后仍能正常工作。

此 API 会取消当前正在对产品/服务进行的操作。 使用[检索操作 API](./cloud-partner-portal-api-retrieve-operations.md) 获取要传递给此 API 的 `operationId`。 取消通常是同步操作，但是在某些复杂情况下，可能需要执行新操作来取消现有操作。 在这种情况下，HTTP 响应正文包含应该用于查询状态的操作位置。

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 参数

--------------

|  **名称**    |      **说明**                                  |    **Data type**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  发布者标识符，例如 `contoso`         |   String          |
| offerId      |  产品/服务标识符                                     |   String          |
| api-version  |  API 的当前版本                               |    日期           |
|  |  |  |

## <a name="header"></a>Header
------

|  **名称**              |  **值**         |
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

#### <a name="migrated-offers"></a>已迁移的产品/服务

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>未迁移的产品/服务

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>响应标头

|  **名称**             |    **值**                       |
|  ---------            |    ----------                      |
| 位置    | 用于检索此操作的状态的相对路径。 |
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
