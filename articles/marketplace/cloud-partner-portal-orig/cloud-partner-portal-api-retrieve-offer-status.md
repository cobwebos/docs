---
title: 检索产品/服务状态 | Microsoft Docs
description: API 可检索产品/服务的当前状态。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 9233a5919ad86adcbb7947cd095945654ed015a7
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805791"
---
<a name="retrieve-offer-status"></a>检索产品/服务状态 
=====================

检索产品/服务的当前状态。

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>URI 参数
--------------

|  **名称**       |   **说明**                            |  **数据类型** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | 发布者标识符，例如 `Contoso`  |     String     |
|  offerId        | 唯一标识产品/服务的 GUID      |     String     |
|  api-version    | API 的最新版本                        |     日期       |
|  |  |


<a name="header"></a>标头
------

|  名称           |  值               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  授权  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>正文示例
------------

### <a name="response"></a>响应

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
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
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>响应正文属性

|  **名称**             |    **说明**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | 产品/服务的状态。 有关可能值的列表，请参阅下面的[产品/服务状态](#offer-status)。 |
|  messages             | 与产品/服务关联的消息数组                                                    |
|  steps                | 产品/服务在发布期间所经历的一系列步骤                      |
|  estimatedTimeFrame   | 以友好的格式估算完成此步骤所需的时间                       |
|  id                   | 步骤的标识符                                                                         |
|  stepName             | 步骤的名称                                                                               |
|  description          | 步骤的说明                                                                        |
|  status               | 步骤的状态。 有关可能值的列表，请参阅下面的[步骤状态](#step-status)。    |
|  messages             | 与步骤相关的消息数组                                                          |
|  processPercentage    | 步骤的完成百分比                                                              |
|  previewLinks         | *当前未实现*                                                                    |
|  liveLinks            | *当前未实现*                                                                    |
|  notificationEmails   | 逗号分隔的电子邮件地址列表，用于通知操作进度        |
|  |  |


### <a name="response-status-codes"></a>响应状态代码

| **代码** |   **说明**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` - 请求已成功处理，并且已返回产品/服务的当前状态。 |
|  400     | `Bad/Malformed request` - 错误响应正文可能包含更多信息。                 |
|  404     | `Not found` - 指定的实体不存在。                                                |
|  |  |


### <a name="offer-status"></a>产品/服务状态

|  **名称**                    |    **说明**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | 产品/服务从未发布过。                          |
|  NotStarted                  | 产品/服务是新的，且未启动。                            |
|  WaitingForPublisherReview   | 产品/服务正在等待发布者批准。                 |
|  正在运行                     | 正在处理产品/服务提交。                     |
|  已成功                   | 产品/服务提交已完成处理。               |
|  已取消                    | 产品/服务提交已取消。                           |
|  已失败                      | 产品/服务提交失败。                                 |
|  |  |


### <a name="step-status"></a>步骤状态

|  **名称**                    |    **说明**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | 步骤尚未开始。                        |
|  InProgress                  | 步骤正在运行。                             |
|  WaitingForPublisherReview   | 步骤正在等待发布者批准。      |
|  WaitingForApproval          | 步骤正在等待流程审批。        |
|  已阻止                     | 步骤被阻止。                             |
|  已拒绝                    | 步骤被拒绝。                            |
|  完成                    | 步骤已完成。                            |
|  已取消                    | 步骤已取消。                           |
|  |  |
