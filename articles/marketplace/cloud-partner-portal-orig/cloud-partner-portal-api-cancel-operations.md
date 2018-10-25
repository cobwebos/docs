---
title: 取消操作 API | Microsoft Docs
description: 取消操作。
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
ms.openlocfilehash: 18f00391beded0744c80eab73bb1efe1c6ab8dbc
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805231"
---
<a name="cancel-operation"></a>取消操作 
=================

此 API 会取消当前正在对产品/服务进行的操作。 使用[检索操作 API](./cloud-partner-portal-api-retrieve-operations.md) 获取要传递给此 API 的 `operationId`。 取消通常是同步操作，但是在某些复杂情况下，可能需要执行新操作来取消现有操作。 在这种情况下，HTTP 响应正文包含应该用于查询状态的操作位置。

你可以使用请求提供以逗号分隔的电子邮件地址列表，此 API 将通知这些地址有关操作的进度。

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI 参数
--------------

|  **名称**    |      **说明**                                  |    **数据类型**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  发布者标识符，例如 `contoso`         |   String          |
| offerId      |  产品/服务标识符                                     |   String          |
| api-version  |  API 的当前版本                               |    日期           |
|  |  |  |


<a name="header"></a>标头
------

|  **名称**              |  **值**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  授权         |  持有者令牌 |
|  |  |


<a name="body-example"></a>正文示例
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

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>响应标头

|  **名称**             |    **值**                       |
|  ---------            |    ----------                      |
| Operation-Location    | URL，可以查询它以确定操作的当前状态。 |
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
