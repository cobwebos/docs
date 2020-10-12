---
title: 检索操作 API-Azure Marketplace
description: 用于检索对产品/服务的所有操作或为指定的 operationId 获取特定操作的 API。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: cb44d977407a7e854603e6bbacf3591752b109c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87271937"
---
# <a name="retrieve-operations"></a>检索操作

> [!NOTE]
> 云合作伙伴门户 Api 与集成，并将在合作伙伴中心继续工作。 转换引入了少量更改。 查看 [云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md) 中列出的更改，确保你的代码在转换到合作伙伴中心后继续工作。 CPP Api 仅适用于过渡到合作伙伴中心之前已集成的现有产品;新产品应使用合作伙伴中心提交 Api。

检索针对产品/服务的所有操作或获取指定 operationId 所对应的特定操作。 客户端可以使用查询参数来筛选正在运行的操作。

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>URI 参数

|  **名称**          |      **描述**                                                                                           | **Data type** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  发布者标识符，例如 `Contoso`                                                                   |  字符串       |
|  offerId           |  产品/服务标识符                                                                                              |  字符串       |
|  operationId       |  唯一标识针对产品/服务的操作的 GUID。 可以使用此 API 检索 operationId，并且对于任何长时间运行的操作（如[发布产品/服务](./cloud-partner-portal-api-publish-offer.md) API），也会在响应的 HTTP 标头中返回 operationId。  |   GUID   |
|  api-version       | API 的最新版本 |    Date      |
|  |  |  |

## <a name="header"></a>Header

|  **Name**          |  **值**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  授权     | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>正文示例

### <a name="response"></a>响应

#### <a name="get-operations"></a>GET 操作

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>GET 操作

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>响应正文属性

|  **名称**                    |  **描述**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | 唯一标识操作的 GUID                                                       |
|  submissionType              | 标识为产品/服务报告的操作类型，例如 `Publish/GoLive`      |
|  createdDateTime             | 创建操作时的 UTC 日期/时间                                                       |
|  lastActionDateTime          | 上次更新操作时的 UTC 日期/时间                                       |
|  status                      | 操作的状态 `not started` \| `running` \| `failed` \| `completed` 。 一次只能有一个操作具有状态 `running`。 |
|  error                       | 操作失败的错误消息                                                               |
|  |  |

### <a name="response-step-properties"></a>响应步骤属性

|  **名称**                    |  **描述**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| estimatedTimeFrame | 此操作的估计持续时间 |
| id | 步骤过程的唯一标识符 |
| description | 步骤的说明 |
| stepName | 步骤的友好名称 |
| status | 步骤的状态，可以是 `notStarted` \| `running` \| `failed` \|`completed` |
| messages | 在此步骤中遇到的任何通知或警告。 字符串数组 |
| System.componentmodel.progresschangedeventargs.progresspercentage | 0到100之间的一个整数，用于指示步骤的进度 |
| | |

### <a name="response-status-codes"></a>响应状态代码

| **代码**  |   **描述**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` - 请求已成功处理，并返回了请求的操作。        |
|  400      | `Bad/Malformed request` - 错误响应正文可能包含更多信息。                    |
|  403      | `Forbidden` - 客户端无法访问指定的命名空间。                          |
|  404      | `Not found` - 指定的实体不存在。                                                 |
|  |  |
