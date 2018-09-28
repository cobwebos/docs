---
title: 在国家云中使用 Azure AD 的身份验证
description: 了解国家云的应用注册和身份验证终结点。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981995"
---
# <a name="national-clouds"></a>国家云

国家云（也称为主权云）是 Azure 的物理独立实例。 Azure 的这些区域旨在确保数据驻留、主权和合规性要求在地理边界内得到遵从。

包括全球云，Azure ActiveDirectory 在以下国家云中部署：  

- Azure 美国政府版
- Azure 德国
- Azure 中国世纪互联

## <a name="app-registration-endpoints"></a>应用注册终结点

下表列出了用于为每个国家云注册应用程序的 Azure Active Directory (Azure AD) 终结点的基 URL。

| 国家云 | Azure AD 门户终结点
| --- | --- |
| 适用于美国政府的 Azure AD |https://portal.azure.us
|Azure AD 德国 |https://portal.microsoftazure.de
|由世纪互联运营的 Azure AD 中国 |https://portal.azure.cn
|Azure AD（全局服务）|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 身份验证终结点

下表列出了用于获取令牌以针对每个国家云调用 Microsoft Graph 的 Azure Active Directory (Azure AD) 终结点的基 URL。

| 国家云 | Azure AD 身份验证终结点
| --- | --- |
| 适用于美国政府的 Azure AD |`https://login.microsoftonline.us`
|Azure AD 德国| `https://login.microsoftonline.de`
|由世纪互联运营的 Azure AD 中国 | `https://login.chinacloudapi.cn`
|Azure AD（全局服务）|`https://login.microsoftonline.com`

可以使用适当的特定于区域的基 URL 来形成对 Azure AD 授权或令牌终结点的请求。 例如，就德国而言：

- 授权常用终结点为 `https://login.microsoftonline.de/common/oauth2/authorize`
- 令牌常用终结点为 `https://login.microsoftonline.de/common/oauth2/token` 

对于单租户应用程序，请将上述 URL 中的 common 替换为你的租户 ID 或名称，例如 `https://login.microsoftonline.de/contoso.com`

>[!NOTE]
> [Azure AD v2.0 授权]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview)和令牌终结点仅适用于全局服务。 国家云部署尚不支持它。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 政府](https://docs.microsoft.com/azure/azure-government/)
- 详细了解 [Azure 中国世纪互联](https://docs.microsoft.com/azure/china/)
- 详细了解 [Azure 德国](https://docs.microsoft.com/azure/germany/)
- 了解 [Azure AD 身份验证基础知识](authentication-scenarios.md)
- 详细了解[国家云中的 Microsoft Graph 部署](https://developer.microsoft.com/graph/docs/concepts/deployments)