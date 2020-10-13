---
title: 检索提供 API-Azure Marketplace
description: 用于检索发布服务器命名空间下的产品/服务的汇总列表的 API。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 39d07751c708d5555799ecbb3b3bc66d3f44f43a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87271954"
---
# <a name="retrieve-offers"></a>检索产品/服务

> [!NOTE]
> 云合作伙伴门户 Api 与集成，并将在合作伙伴中心继续工作。 转换引入了少量更改。 查看 [云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md) 中列出的更改，确保你的代码在转换到合作伙伴中心后继续工作。 CPP Api 仅适用于过渡到合作伙伴中心之前已集成的现有产品;新产品应使用合作伙伴中心提交 Api。

检索发布者命名空间下的产品/服务汇总列表。

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 参数

| **名称**         |  **描述**                         |  **Data type** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | 发布者标识符，例如 `contoso` |   字符串    |
|  api-version     | API 的最新版本                    |    Date        |
|  |  |

## <a name="header"></a>Header

|  **Name**        |         **值**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  授权   | `Bearer YOUR_TOKEN`     |
|  |  |

## <a name="body-example"></a>正文示例

### <a name="response"></a>响应

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>响应正文属性

|  **名称**       |       **描述**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | 标识产品/服务的类型                                                                                           |
|  publisherId    | 唯一标识发布者的标识符                                                                      |
|  status         | 产品/服务的状态。 有关可能值的列表，请参阅下面的[产品/服务状态](#offer-status)。                         |
|  id             | 唯一标识发布者命名空间中的产品/服务的 GUID。                                                    |
|  版本        | 该产品/服务的当前版本。 客户端无法修改版本属性。 它在每次发布后都会递增。 |
|  定义     | 包含工作负载实际定义的摘要视图。 若要获取详细定义，请使用[检索特定产品/服务](./cloud-partner-portal-api-retrieve-specific-offer.md) API。 |
|  changedTime    | 上次修改该产品/服务时的 UTC 时间                                                                              |
|  |  |

### <a name="response-status-codes"></a>响应状态代码

| **代码**  |  **描述**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - 已成功处理请求，发布者下的所有产品/服务都已返回给客户端。  |
|  400      | `Bad/Malformed request` - 错误响应正文可能包含更多信息。                                    |
|  403      | `Forbidden` - 客户端无法访问指定的命名空间。                                          |
|  404      | `Not found` - 指定的实体不存在。                                                                 |
|  |  |

### <a name="offer-status"></a>产品/服务状态

|  **名称**                    | **描述**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | 产品/服务从未发布过。                  |
|  NotStarted                  | 产品/服务是新的，但未启动。                 |
|  WaitingForPublisherReview   | 产品/服务正在等待发布者批准。         |
|  运行                     | 正在处理产品/服务提交。             |
|  已成功                   | 产品/服务提交已完成处理。       |
|  已取消                    | 产品/服务提交已取消。                   |
|  已失败                      | 产品/服务提交失败。                         |
|  |  |
