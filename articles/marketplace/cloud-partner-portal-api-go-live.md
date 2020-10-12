---
title: 上线 API-Azure Marketplace
description: 上线 API 启动产品/服务上线一览过程。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: d612b796f85c9eaab1600c55cde7e79acb49f352
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292933"
---
# <a name="go-live"></a>上线

> [!NOTE]
> 云合作伙伴门户 Api 与集成，并将在合作伙伴中心继续工作。 转换引入了少量更改。 查看 [云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md) 中列出的更改，确保你的代码在转换到合作伙伴中心后继续工作。 CPP Api 仅适用于过渡到合作伙伴中心之前已集成的现有产品;新产品应使用合作伙伴中心提交 Api。

此 API 启动将应用投入生产的过程。 这通常是一项长期操作。 此调用使用[发布](./cloud-partner-portal-api-publish-offer.md) API 操作返回的通知电子邮件列表。

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI 参数
--------------

|  **名称**      |   **描述**                                                           | **Data type** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 要检索的产品/服务的发布者标识符，例如 `contoso`       |  字符串       |
| offerId        | 要检索的产品/服务的产品/服务标识符                                   |  字符串       |
| api-version    | API 最新版本                                                   |  Date         |
|  |  |  |

## <a name="header"></a>Header
------

|  **Name**       |     **值**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| 授权   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>正文示例

### <a name="response"></a>响应

#### <a name="migrated-offers"></a>已迁移的产品/服务

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>未迁移的产品/服务

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>响应标头

|  **Name**             |      **值**                                                            |
|  --------             |      ----------                                                           |
| 位置    |  用于检索此操作的状态的相对路径            |
|  |  |

### <a name="response-status-codes"></a>响应状态代码

| **代码** |  **描述**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` - 已成功接受请求。 响应包含用于跟踪操作状态的位置。 |
|  400     | `Bad/Malformed request` - 响应正文中的其他错误信息。 |
|  404     |  `Not found` - 指定的实体不存在。                                       |
|  |  |
