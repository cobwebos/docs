---
title: Azure AD 身份验证和国家云 | Azure
titleSuffix: Microsoft identity platform
description: 了解国家云的应用注册和身份验证终结点。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: 20a158aac6a03dfe0bd8929de9fa66ea215aa29c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194116"
---
# <a name="national-clouds"></a>国家云

国家云是 Azure 的物理独立实例。 Azure 的这些区域旨在确保数据驻留、主权和合规性要求在地理边界内得到遵从。

包括全球云，Azure Active Directory (Azure AD) 在以下国家云中部署：  

- Azure Government
- Azure 德国
- Azure 中国世纪互联

国家云是唯一的，不同于 Azure 全局环境。 针对这些环境开发应用程序时，请务必了解关键差异。 差异包括应用程序的注册、令牌的获取和终结点的配置。

## <a name="app-registration-endpoints"></a>应用注册终结点

每个国家云都有一个单独的 Azure 门户。 若要在国家云中将应用程序与 Microsoft 标识平台集成，则需要在每个特定于环境的 Azure 门户中单独注册应用程序。

下表列出了用于为每个国家云注册应用程序的 Azure AD 终结点的基 URL。

| 国家云 | Azure AD 门户终结点 |
|----------------|--------------------------|
| 适用于美国政府的 Azure AD | `https://portal.azure.us` |
| Azure AD 德国 | `https://portal.microsoftazure.de` |
| 由世纪互联运营的 Azure AD 中国 | `https://portal.azure.cn` |
| Azure AD（全局服务） |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 身份验证终结点

所有各国云在每个环境中分别对用户进行身份验证，并具有单独的身份验证终结点。

下表列出了用于获取令牌以针对每个国家云的 Azure AD 终结点的基 URL。

| 国家云 | Azure AD 身份验证终结点 |
|----------------|-------------------------|
| 适用于美国政府的 Azure AD | `https://login.microsoftonline.us` |
| Azure AD 德国| `https://login.microsoftonline.de` |
| 由世纪互联运营的 Azure AD 中国 | `https://login.chinacloudapi.cn` |
| Azure AD（全局服务）| `https://login.microsoftonline.com` |

可以使用适当的特定于区域的基 URL 来形成对 Azure AD 授权或令牌终结点的请求。 例如，对于 Azure 德国：

  - 授权常用终结点为 `https://login.microsoftonline.de/common/oauth2/v2.0/authorize`。
  - 令牌常用终结点为 `https://login.microsoftonline.de/common/oauth2/v2.0/token`。

对于单租户应用程序，请将先前 URL 中的 common 替换为你的租户 ID 或名称。 示例为 `https://login.microsoftonline.de/contoso.com`。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

若要了解如何在国家云环境中调用 Microsoft Graph API，请转到[国家云部署中的 Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments)。

> [!IMPORTANT]
> 全局服务的特定区域中的某些服务和功能可能无法在所有国家云中使用。 若要了解哪些服务可用，请访问[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)。

若要了解如何使用 Microsoft 标识平台构建应用程序，请遵循 [Microsoft 身份验证库 (MSAL) 教程](msal-national-cloud.md)。 具体而言，此应用会将用户登录，获取用于调用 Microsoft Graph API 的访问令牌。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure 中国世纪互联](https://docs.microsoft.com/azure/china/)
- [Azure 德国](https://docs.microsoft.com/azure/germany/)
- [Azure AD 身份验证基础知识](authentication-scenarios.md)
