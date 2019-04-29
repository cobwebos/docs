---
title: API 注意事项 | Microsoft Docs
description: 使用市场 API 时的版本控制、错误处理和授权问题。
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
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c6cfb41cb6254145821ab3fef662e9a5e54f6298
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625051"
---
<a name="api-considerations"></a>API 注意事项
=================

<a name="api-versioning"></a>API 版本控制
--------------

可能有多个版本的 API 同时可用。 客户端必须通过提供 `api-version` 参数作为查询字符串的一部分来指明它们希望调用哪个版本。

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

对包含未知或无效 API 版本的请求的响应是 HTTP 代码 400。 此错误会在响应正文中返回已知 API 版本的集合。

``` json
    {
        "error”: { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>错误
------

API 使用相应的 HTTP 状态代码响应错误，并且可选地将响应中的其他信息序列化为 JSON。
当你收到错误（尤其是 400 类错误）时，请不要在修复底层原因之前重试请求。 例如，在上面的示例响应中，在重新发送请求之前修复 API 版本参数。

<a name="authorization-header"></a>授权标头
--------------------

对于本参考中的所有 API，必须传递授权标头以及从 Azure Active Directory (Azure AD) 获取的持有者令牌。 此标头是接收有效响应所必需的；如果不存在，则返回 `401 Unauthorized` 错误。 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
