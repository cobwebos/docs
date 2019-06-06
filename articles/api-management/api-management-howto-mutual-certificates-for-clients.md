---
title: 使用 API 管理中的客户端证书身份验证确保 API 安全 - Azure API 管理 | Microsoft 文档
description: 了解如何使用客户端证书保护对 API 的访问
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427345"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的客户端证书身份验证确保 API 安全

API 管理提供的功能可确保使用客户端证书安全地访问 API（即，客户端到 API 管理）。 可以验证传入证书，并检查证书属性，针对所需使用策略表达式的值。

有关保护使用客户端证书 （即，API 管理到后端） 的 API 后端服务的访问权限的信息，请参阅[如何保护后端服务使用客户端证书身份验证](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> 若要接收并验证在消耗层中的客户端证书必须首先启用"请求客户端证书"设置"自定义域"边栏选项卡上，如下所示。

![请求客户端证书](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>检查颁发者和使用者

可以将以下策略配置为检查客户端证书的颁发者和使用者：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 若要禁用检查证书吊销列表使用`context.Request.Certificate.VerifyNoRevocation()`而不是`context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名证书，根 （或中间） 必须是 CA 证书[上传](api-management-howto-ca-certificates.md)为 API 管理`context.Request.Certificate.Verify()`和`context.Request.Certificate.VerifyNoRevocation()`工作。

## <a name="checking-the-thumbprint"></a>检查指纹

可以将以下策略配置为检查客户端证书的指纹：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 若要禁用检查证书吊销列表使用`context.Request.Certificate.VerifyNoRevocation()`而不是`context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名证书，根 （或中间） 必须是 CA 证书[上传](api-management-howto-ca-certificates.md)为 API 管理`context.Request.Certificate.Verify()`和`context.Request.Certificate.VerifyNoRevocation()`工作。

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>针对已上传到 API 管理的证书检查指纹

以下示例演示如何针对已上传到 API 管理的证书，检查客户端证书的指纹：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> 若要禁用检查证书吊销列表使用`context.Request.Certificate.VerifyNoRevocation()`而不是`context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名证书，根 （或中间） 必须是 CA 证书[上传](api-management-howto-ca-certificates.md)为 API 管理`context.Request.Certificate.Verify()`和`context.Request.Certificate.VerifyNoRevocation()`工作。

> [!TIP]
> 客户端证书死锁问题中所述[一文](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)可能会出现几种方法，例如请求冻结，请求结果`403 Forbidden`后不会超时，状态代码`context.Request.Certificate`是`null`。 此问题通常会影响`POST`和`PUT`请求的内容长度大约 60 kb 或更大。
> 若要防止从发生此问题启用"协商客户端证书"设置为所需的主机名，在"自定义域"边栏选项卡，如下所示。 此功能不消耗层中可用。

![协商客户端证书](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>后续步骤

-   [如何使用客户端证书身份验证确保后端服务安全](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [如何上传证书](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
