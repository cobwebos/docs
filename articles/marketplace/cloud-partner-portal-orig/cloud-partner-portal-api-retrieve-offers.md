---
title: 检索产品/服务 API | Microsoft Docs
description: API 检索发布者命名空间下的产品/服务汇总列表。
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
ms.openlocfilehash: de9261548ec79e206b0db87caabc1fa4c9ad6771
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094490"
---
<a name="retrieve-offers"></a>检索产品/服务
===============

检索发布者命名空间下的产品/服务汇总列表。

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>URI 参数
--------------

| **名称**         |  **说明**                         |  **数据类型** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | 发布者标识符，例如 `contoso` |   String    |
|  api-version     | API 的最新版本                    |    date        |
|  |  |


<a name="header"></a>标头
------

|  **名称**        |         **值**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  授权   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>正文示例
------------

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

|  **名称**       |       **说明**                                                                                                  |
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

| **代码**  |  **说明**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - 已成功处理请求，发布者下的所有产品/服务都已返回给客户端。  |
|  400      | `Bad/Malformed request` - 错误响应正文可能包含更多信息。                                    |
|  403      | `Forbidden` - 客户端无法访问指定的命名空间。                                          |
|  404      | `Not found` - 指定的实体不存在。                                                                 |
|  |  |


### <a name="offer-status"></a>产品/服务状态

|  **名称**                    | **说明**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | 产品/服务从未发布过。                  |
|  NotStarted                  | 产品/服务是新的，但未启动。                 |
|  WaitingForPublisherReview   | 产品/服务正在等待发布者批准。         |
|  正在运行                     | 正在处理产品/服务提交。             |
|  已成功                   | 产品/服务提交已完成处理。       |
|  已取消                    | 产品/服务提交已取消。                   |
|  已失败                      | 产品/服务提交失败。                         |
|  |  |
