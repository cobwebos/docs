---
title: 在 API 管理中使用客户端证书身份验证保护 Api
titleSuffix: Azure API Management
description: 클라이언트 인증서를 사용하여 API에 대한 액세스의 보안을 유지하는 방법을 알아봅니다.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713147"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management에서 클라이언트 인증서 인증을 사용하여 API를 보호하는 방법

API Management에서는 클라이언트 인증서를 사용하여 API에 대한 액세스(예: API Management에 대한 클라이언트)를 보호하는 기능을 제공합니다. 您可以使用策略表达式来验证传入证书，并根据所需值检查证书属性。

有关使用客户端证书保护对 API 后端服务的访问（即，API 管理到后端）的信息，请参阅[如何使用客户端证书身份验证保护后端服务](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> 若要在开发人员、基本、标准或高级层中通过 HTTP/2 接收和验证客户端证书，必须在 "自定义域" 边栏选项卡上打开 "协商客户端证书" 设置，如下所示。

![协商客户端证书](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> 若要接收和验证消耗层中的客户端证书，必须在 "自定义域" 边栏选项卡上打开 "请求客户端证书" 设置，如下所示。

![请求客户端证书](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>발급자 및 주체 확인

클라이언트 인증서의 발급자 및 주체를 확인하도록 아래 정책을 구성할 수 있습니다.

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
> 若要禁用检查证书吊销列表，请使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名的，则必须将根（或中间） CA 证书[上传](api-management-howto-ca-certificates.md)到 API 管理，以便 `context.Request.Certificate.Verify()` 和 `context.Request.Certificate.VerifyNoRevocation()` 工作。

## <a name="checking-the-thumbprint"></a>지문 확인

클라이언트 인증서의 지문을 확인하도록 아래 정책을 구성할 수 있습니다.

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
> 若要禁用检查证书吊销列表，请使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名的，则必须将根（或中间） CA 证书[上传](api-management-howto-ca-certificates.md)到 API 管理，以便 `context.Request.Certificate.Verify()` 和 `context.Request.Certificate.VerifyNoRevocation()` 工作。

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management에 업로드된 인증서에 대해 지문 확인

다음 예제에서는 API Management에 업로드된 인증서에 대해 클라이언트 인증서의 지문을 확인하는 방법을 보여 줍니다.

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
> 若要禁用检查证书吊销列表，请使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名的，则必须将根（或中间） CA 证书[上传](api-management-howto-ca-certificates.md)到 API 管理，以便 `context.Request.Certificate.Verify()` 和 `context.Request.Certificate.VerifyNoRevocation()` 工作。

> [!TIP]
> [本文](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)中所述的客户端证书死锁问题可以通过多种方式（例如，请求冻结，请求导致在超时后 `403 Forbidden` 状态代码，`context.Request.Certificate` 为 `null`。 此问题通常会影响 `POST` 和 `PUT` 请求，其内容长度约为60KB 或更大。
> 若要防止出现此问题，请在 "自定义域" 边栏选项卡上打开所需主机名的 "协商客户端证书" 设置，如下所示。 此功能在消耗层中不可用。

![协商客户端证书](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>다음 단계

-   [클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [인증서 업로드 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
