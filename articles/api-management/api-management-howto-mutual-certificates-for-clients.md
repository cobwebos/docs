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
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 450ebc621758363c5ea9ab6d631cd6c7df38794b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657707"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的客户端证书身份验证确保 API 安全

API 管理提供的功能可确保使用客户端证书安全地访问 API（即，客户端到 API 管理）。 目前，可以针对所需值检查客户端证书的指纹。 还可以针对已上传到 API 管理的现有证书检查指纹。  

有关使用客户端证书保护对 API 后端服务的访问（即，API 管理到后端）的信息，请参阅[如何使用客户端证书身份验证保护后端服务](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="checking-the-expiration-date"></a>检查到期日期

可以将以下策略配置为检查证书是否过期：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>检查颁发者和使用者

可以将以下策略配置为检查客户端证书的颁发者和使用者：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>检查指纹

可以将以下策略配置为检查客户端证书的指纹：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>针对已上传到 API 管理的证书检查指纹

以下示例演示如何针对已上传到 API 管理的证书，检查客户端证书的指纹： 

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>后续步骤

*  [如何使用客户端证书身份验证确保后端服务安全](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [如何上传证书](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

