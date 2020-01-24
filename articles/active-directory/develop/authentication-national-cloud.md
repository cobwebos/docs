---
title: Azure AD 身份验证 & 国家云 |Microsoft
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
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698010"
---
# <a name="national-clouds"></a>国家/地区云

国家云是 Azure 的物理上独立的实例。 Azure 的这些区域旨在确保数据驻留、主权和合规性要求在地理边界内得到遵从。

在以下国家/地区中部署 Azure Active Directory （Azure AD）包括全局云：  

- Azure Government
- Azure Germany
- Azure 中国世纪互联

国家云是独特的，独立于 Azure global 环境。 在为这些环境开发应用程序时，请务必了解关键区别。 差异包括注册应用程序、获取令牌和配置终结点。

## <a name="app-registration-endpoints"></a>应用注册终结点

对于每个国家云，都有一个单独的 Azure 门户。 若要将应用程序与全国云中的 Microsoft 标识平台集成，需要在每个特定于环境的 Azure 门户中单独注册应用程序。

下表列出了用于为每个国家云注册应用程序的 Azure AD 终结点的基 Url。

| 国家云 | Azure AD 门户终结点 |
|----------------|--------------------------|
| 适用于美国政府的 Azure AD | `https://portal.azure.us` |
| Azure AD 德国 | `https://portal.microsoftazure.de` |
| 由世纪互联运营的 Azure AD 中国 | `https://portal.azure.cn` |
| Azure AD（全局服务） |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 身份验证终结点

所有各国云在每个环境中分别对用户进行身份验证，并具有单独的身份验证终结点。

下表列出了用于获取每个国家云的令牌的 Azure AD 终结点的基 Url。

| 国家云 | Azure AD 身份验证终结点 |
|----------------|-------------------------|
| 适用于美国政府的 Azure AD | `https://login.microsoftonline.us` |
| Azure AD 德国| `https://login.microsoftonline.de` |
| 由世纪互联运营的 Azure AD 中国 | `https://login.chinacloudapi.cn` |
| Azure AD（全局服务）| `https://login.microsoftonline.com` |

你可以通过使用相应的区域特定的基 URL，将请求发送到 Azure AD 授权或令牌终结点。 例如，对于 Azure 德国：

  - 授权常用终结点为 `https://login.microsoftonline.de/common/oauth2/authorize`。
  - 令牌常用终结点为 `https://login.microsoftonline.de/common/oauth2/token`。

对于单租户应用程序，请将之前 Url 中的 "common" 替换为你的租户 ID 或名称。 示例为 `https://login.microsoftonline.de/contoso.com`。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

若要了解如何在国家/地区云环境中调用 Microsoft Graph Api，请参阅[国内云部署 Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments)。

> [!IMPORTANT]
> 全局服务的特定区域中的某些服务和功能可能在所有的国家云中都不可用。 若要了解哪些服务可用，请访问[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)。

若要了解如何使用 Microsoft 标识平台构建应用程序，请按照[Microsoft 身份验证库（MSAL）教程](msal-national-cloud.md)操作。 具体而言，此应用将登录用户并获取访问令牌，以调用 Microsoft Graph API。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure 中国世纪互联](https://docs.microsoft.com/azure/china/)
- [Azure 德国](https://docs.microsoft.com/azure/germany/)
- [Azure AD 身份验证基础知识](authentication-scenarios.md)
