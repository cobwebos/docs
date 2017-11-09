---
title: "自定义连接器常见问题解答 - Azure 逻辑应用 | Microsoft Docs"
description: "有关创建自定义连接器的相关要求和触发器等常见问题解答"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>常见问题解答：自定义连接器

## <a name="requirements"></a>要求

**问：**是否可以在没有 REST API 的情况下生成连接器？ </br>
**答：**不可以。若要生成连接器，必须支持适用于服务的稳定 HTTP REST API。 

**问：**可以使用哪些工具来创建连接器？ </br>
**答：**Azure 具有可用于将任何服务公开为 API 的功能和服务，例如用于托管的 Azure App Service，以及 API 管理等等。

**问：**支持哪些身份验证类型？ </br>
**答：**可使用以下受支持的身份验证标准：

* [OAuth 2.0](https://oauth.net/2/)，包括 [Azure Active Directory](https://azure.microsoft.com/develop/identity/) 或 Dropbox、GitHub 和 SalesForce 等特定服务
* 通用 OAuth 2.0
* [基本身份验证](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [API 密钥](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>触发器

**问：**是否可以在没有 webhook 的情况下生成触发器？ </br>
**答：**不可以。Azure 逻辑应用和 Microsoft Flow 适用的自定义连接器仅支持基于 webhook 的触发器。 如果想请求实现其他的模式，请发送电子邮件至 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)，并附上你 API 的更多详细信息。

## <a name="certification"></a>认证

**问：**我不是 Microsoft 合作伙伴或独立软件供应商 (ISV)。 是否仍然可以创建连接器？ </br>
**答：**可以。你可以注册这些连接器供组织内部使用，但如果想认证和公开发布连接器，则必须具有基础服务或现有的显式权限才能使用 API。

## <a name="other"></a>其他

**问：**我的 API 使用动态主机。 如何使用 OpenAPI 实现它们？ </br>
**答：**自定义连接器不支持动态主机。 请改用静态主机进行开发和测试。 如果想要认证连接器，请询问 Microsoft 联系人，了解动态实现的相关信息。

**问：**是否支持 Postman Collection V2？ </br>
**答：**不支持。当前仅支持 Postman Collection V1。

**问：**是否支持 OpenAPI 3.0？ </br>
**答：**不支持。当前仅支持 OpenAPI 2.0。