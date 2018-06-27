---
title: 使用客户端证书身份验证保护后端服务 - Azure API 管理 | Microsoft 文档
description: 了解如何使用 Azure API 管理中的客户端证书身份验证确保后端服务安全。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 844a7ea1c2dd8f7dbb4984fc148575529ac154db
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292851"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>如何使用 Azure API 管理中的客户端证书身份验证确保后端服务安全

API 管理允许使用客户端证书保护对 API 后端服务的访问。 本指南介绍如何在 Azure 门户的 Azure API 管理服务实例中管理证书。 它还说明了如何配置 API 以使用证书来访问后端服务。

有关如何使用 API 管理 REST API 来管理证书的信息，请参阅 <a href="https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API 管理 REST API 证书实体</a>。

## <a name="prerequisites"> </a>先决条件

本指南介绍如何将 API 管理服务实例配置为使用客户端证书身份验证访问 API 的后端服务。 在按照本文中的步骤操作之前，应该为后端服务配置客户端证书身份验证（[若要在 Azure 网站中配置证书身份验证，请参阅此文][to configure certificate authentication in Azure WebSites refer to this article]）。 你需要访问证书和密码才能将其上传到 API 管理服务。

## <a name="step1"></a>上传客户端证书

![添加客户端证书](media/api-management-howto-mutual-certificates/apim-client-cert.png)

请按照以下步骤来上传新的客户端证书。 如果尚未创建 API 管理服务实例，请参阅教程[创建 API 管理服务实例][Create an API Management service instance]。

1. 在 Azure 门户中导航到 Azure API 管理服务实例。
2. 从菜单中选择“客户端证书”。
3. 单击“**+ 添加**”按钮。  
    ![添加客户端证书](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. 浏览证书，提供其 ID 和密码。  
5. 单击“创建”。

> [!NOTE]
> 证书必须采用 **.pfx** 格式。 允许使用自签名证书。

证书在上传后显示在“客户端证书”中。  如果有多个证书，请记下所需证书的指纹，以便[将 API 配置为使用客户端证书进行网关身份验证][Configure an API to use a client certificate for gateway authentication]。

> [!NOTE]
> 若要在使用某个证书（例如自签名证书）时关闭证书链验证，请执行此常见问题[项](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)中描述的步骤。

## <a name="step1a"> </a>删除客户端证书

若要删除证书，请单击上下文菜单“...”并选择该证书旁边的“删除”。

![删除客户端证书](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

如果证书被某个 API 使用，则会显示警告屏幕。 若要删除证书，必须先将其从配置为使用该证书的 API 中删除。

![删除客户端证书失败](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>将 API 配置为使用客户端证书进行网关身份验证

1. 单击左侧“API 管理”菜单中的“API”，然后导航至 API。  
    ![启用客户端证书](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. 在“设计”选项卡上，单击“后端”部分的铅笔图标。 
3. 将“网关凭据”更改为“客户端证书”，然后从下拉列表中选择证书。  
    ![启用客户端证书](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. 单击“ **保存**”。 

> [!WARNING]
> 此更改立即生效，调用对该 API 的操作时，会使用证书在后端服务器上进行身份验证。


> [!TIP]
> 为 API 的后端服务指定网关身份验证的证书时，此证书会成为该 API 的策略的一部分，可以在策略编辑器中查看。

## <a name="self-signed-certificates"></a>自签名证书

如果使用自签名证书，将需要禁用证书链验证使 API 管理能够与后端系统进行通信， 否则，它将返回 500 错误代码。 若要配置此项，可以使用 [`New-AzureRmApiManagementBackend`](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend)（适用于新后端）或 [`Set-AzureRmApiManagementBackend`](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend)（适用于现有后端）PowerShell cmdlet 并将 `-SkipCertificateChainValidation` 参数设置为 `True`。

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
