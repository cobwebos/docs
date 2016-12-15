---
title: "如何在 Azure API 管理中创建 API"
description: "了解如何在 Azure API 管理中创建和配置 API。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 14c20da4-f29f-4b28-bec7-3d4c50b734da
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 67e0007c7a40cf59609b1030b7f0ddbee90fa237


---
# <a name="how-to-create-apis-in-azure-api-management"></a>如何在 Azure API 管理中创建 API
API 管理中的 API 表示一组可由客户端应用程序调用的操作。 在发布者门户中创建新的 API，然后添加所需的操作。 一旦添加操作，该 API 添加到某一产品并可以发布。 发布 API 后，它可供开发人员订阅和使用。

本指南演示此过程的第一步：如何在 API 管理中创建和配置新的 API。 有关添加操作和发布产品的详细信息，请参阅[如何将操作添加到 API][如何将操作添加到 API]以及[如何创建和发布产品][如何创建和发布产品]。

## <a name="create-new-api"> </a>创建新的 API
API 在发布者门户中创建并发布。 若要访问发布者门户，请在 API 管理服务的 Azure 门户中单击“发布者门户”。

![发布者门户][api-management-management-console]

> 如果尚未创建 API 管理服务实例，请参阅 [创建 API 管理服务实例][创建 API 管理服务实例]教程中的[创建 API 管理服务实例][创建 API 管理服务实例]。
> 
> 

单击左侧“API 管理”菜单中的“API”，然后单击“添加 API”。

![创建 API][api-management-create-api]

使用“添加新 API”窗口配置新的 API。

![添加新的 API][api-management-add-new-api]

以下字段用于配置新的 API。

* **Web API 名称**为 API 提供唯一并且描述性的名称。 它显示在开发人员和发布者门户中。
* **Web 服务 URL** 引用执行该 API 的 HTTP 服务。 API 管理将请求转发到此地址。
* **Web API URL 后缀**附加到 API 管理服务的基础 URL。 基础 URL 是常见的由 API 管理服务实例托管的所有 API。 API 管理通过其后缀区分 API，因此后缀对给定发布者上的每个 API 必须唯一。
* **Web API URL 方案**确定哪些协议可用于访问 API。 默认情况下指定 HTTPs。
* 若要（可选）将此新 API 添加到产品，请单击“产品(可选)”下拉列表并选择产品。 多次重复此步骤以将 API 添加到多个产品。

配置所需的值后，单击“保存”。 一旦创建新的 API，该 API 的摘要页显示在发布者门户中。

![API 摘要][api-management-api-summary]

## <a name="configure-api-settings"> </a>配置 API 设置
可以使用“设置”选项卡来验证并编辑一个 API 的配置。 “Web API 名称”、“Web 服务 URL”和“Web API URL 后缀”最初在创建 API 时设置，并且可在此处修改。 “说明”提供可选说明，而“Web API URL 方案”确定哪些协议可用于访问 API。

![API 设置][api-management-api-settings]

若要为实现 API 的后端服务配置网关身份验证，请选择“安全”选项卡。 “使用凭据”下拉列表可用于配置“HTTP BASIC”或“客户端证书”身份验证。 若要使用 HTTP BASIC 身份验证，只需输入所需的凭据。 有关使用客户端证书身份验证的信息，请参阅[如何在 Azure API 管理中使用客户端证书身份验证确保后端服务安全][如何使用 Azure API 管理中的客户端证书身份验证确保后端服务安全]。

“安全”选项卡还可用于使用 OAuth 2.0 配置“用户授权”。 有关详细信息，请参阅[如何在 Azure API 管理中使用 OAuth 2.0 授权开发人员帐户][如何在 Azure API 管理中使用 OAuth 2.0 授权开发人员帐户]。

![基本身份验证设置][api-management-api-settings-credentials]

单击“保存”保存对 API 设置所作的任何更改。

## <a name="next-steps"> </a>后续步骤
一旦创建 API 并配置设置，接下来的步骤是将操作添加到该 API，将 API 添加到产品并将其发布，这样就可用于开发人员。 有关详细信息，请参阅以下文章。

* [如何将操作添加到 API][如何将操作添加到 API]
* [如何创建和发布产品][如何创建和发布产品]

[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[什么是 API？]: #what-is-api
[创建新 API]: #create-new-api
[配置 API 设置]: #configure-api-settings
[配置 API 操作]: #configure-api-operations
[后续步骤]: #next-steps

[如何将操作添加到 API]: api-management-howto-add-operations.md
[如何创建和发布产品]: api-management-howto-add-products.md

[创建 API 管理服务实例]: api-management-get-started.md
[创建 API 管理服务实例]: api-management-get-started.md#create-service-instance
[如何使用 Azure API 管理中的客户端证书身份验证确保后端服务安全]: api-management-howto-mutual-certificates.md
[如何在 Azure API 管理中使用 OAuth 2.0 授权开发人员帐户]: api-management-howto-oauth2.md



<!--HONumber=Nov16_HO3-->


