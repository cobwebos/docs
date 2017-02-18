---
title: "使用 API 管理中的客户端证书身份验证确保 API 安全 - Azure API 管理 | Microsoft 文档"
description: "了解如何使用客户端证书保护对 API 的访问"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: b04ce0fe0db7649cebc7a1eeb2a35f1d53bf9636
ms.openlocfilehash: 1ce9f07c3e91eacdc74ccab3fbb7dc433a25c197

---

# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的客户端证书身份验证确保 API 安全

API 管理提供的功能可确保使用客户端证书安全地访问 API（即，客户端到 API 管理）。 目前，可以针对所需值检查客户端证书的指纹。 还可以针对已上载到 API 管理的现有证书检查指纹。  

有关使用客户端证书保护对 API 后端服务的访问（即，API 管理到后端）的信息，请参阅[如何使用客户端证书身份验证保护后端服务](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-a-thumbprint-against-a-desired-value"></a>针对所需值检查指纹

可以将以下策略配置为检查客户端证书的指纹：

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint-to-validate")" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>针对已上载到 API 管理的证书检查指纹

以下示例演示如何针对已上载到 API 管理的证书，检查客户端证书的指纹： 

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>后续步骤

*  [如何使用客户端证书身份验证确保后端服务安全](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)
*  [如何上载证书](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)




<!--HONumber=Feb17_HO1-->


