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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427345"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的客户端证书身份验证确保 API 安全

API 管理提供的功能可确保使用客户端证书安全地访问 API（即，客户端到 API 管理）。 可以使用策略表达式验证传入证书并根据所需值检查证书属性。

有关保护使用客户端证书 （即，API 管理到后端） 的 API 后端服务的访问权限的信息，请参阅[如何保护后端服务使用客户端证书身份验证](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> 若要在“消耗”层中接收并验证客户端证书，必须先在“自定义域”边栏选项卡上启用“请求客户端证书”设置，如下所示。

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
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
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
> 若要防止出现此问题，请在“自定义域”边栏选项卡上为所需主机名启用“协商客户端证书”设置，如下所示。 在“消耗”层中，此功能不可用。

![协商客户端证书](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>后续步骤

-   [如何使用客户端证书身份验证确保后端服务安全](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [如何上传证书](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
