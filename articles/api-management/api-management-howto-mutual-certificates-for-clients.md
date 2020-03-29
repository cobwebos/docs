---
title: 在 API 管理中使用客户端证书身份验证保护 API
titleSuffix: Azure API Management
description: 了解如何使用客户端证书保护对 API 的访问
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713147"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的客户端证书身份验证确保 API 安全

API 管理提供的功能可确保使用客户端证书安全地访问 API（即，客户端到 API 管理）。 可以使用策略表达式验证传入证书并根据所需值检查证书属性。

有关使用客户端证书（即从后端进行 API 管理）保护对 API 后端服务的访问的信息，请参阅[如何使用客户端证书身份验证保护后端服务](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> 要通过开发人员、基本、标准或高级层中的 HTTP/2 接收和验证客户端证书，必须在"自定义域"边栏选项卡上打开"协商客户端证书"设置，如下所示。

![协商客户端证书](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> 要接收和验证"消耗"层中的客户端证书，必须打开"自定义域"边栏选项卡上的"请求客户端证书"设置，如下所示。

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
> 若要禁止检查证书吊销列表，请使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名证书，则必须将根（或中间）CA 证书[上传](api-management-howto-ca-certificates.md)到 API 管理，`context.Request.Certificate.Verify()` 和 `context.Request.Certificate.VerifyNoRevocation()` 才能正常工作。

## <a name="checking-the-thumbprint"></a>检查指纹

可以将以下策略配置为检查客户端证书的指纹：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 若要禁止检查证书吊销列表，请使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名证书，则必须将根（或中间）CA 证书[上传](api-management-howto-ca-certificates.md)到 API 管理，`context.Request.Certificate.Verify()` 和 `context.Request.Certificate.VerifyNoRevocation()` 才能正常工作。

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
> 若要禁止检查证书吊销列表，请使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名证书，则必须将根（或中间）CA 证书[上传](api-management-howto-ca-certificates.md)到 API 管理，`context.Request.Certificate.Verify()` 和 `context.Request.Certificate.VerifyNoRevocation()` 才能正常工作。

> [!TIP]
> 本[文](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)中所述的客户端证书死锁问题可以通过多种方式表现出来，例如：请求冻结、请求在超时后生成 `403 Forbidden` 状态代码、`context.Request.Certificate` 为 `null`。 此问题通常会影响内容长度约为 60KB 或更大的 `POST` 和 `PUT` 请求。
> 为防止此问题发生，请打开"自定义域"边栏选项卡上所需主机名的"协商客户端证书"设置，如下所示。 在“消耗”层中，此功能不可用。

![协商客户端证书](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>后续步骤

-   [如何使用客户端证书身份验证确保后端服务安全](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [如何上传证书](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
