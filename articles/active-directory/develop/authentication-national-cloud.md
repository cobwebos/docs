---
title: 在国家云中使用 Azure Active Directory 身份验证
description: 了解国家云的应用注册和身份验证终结点。
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235495"
---
# <a name="national-clouds"></a>国家/地区云

国家/地区云是 Azure 的物理独立的实例。 Azure 的这些区域旨在确保数据驻留、主权和合规性要求在地理边界内得到遵从。

全局云，包括 Azure Active Directory (Azure AD) 被部署在以下国家/地区云：  

- Azure Government
- Azure 德国
- Azure 中国世纪互联

是唯一的国家/地区云并从 Azure 全局一个单独的环境。 在开发这些环境中应用程序时需要注意的主要区别至关重要。 区别包括注册应用程序、 获取令牌，并配置终结点。

## <a name="app-registration-endpoints"></a>应用注册终结点

没有为国家/地区云的每个单独的 Azure 门户。 若要在国家/地区云的 Microsoft 标识平台集成的应用程序，您需要在每个特定于环境的 Azure 门户中单独注册你的应用程序。

下表列出了用于注册每个国家/地区云的应用程序的 Azure AD 终结点的基 Url。

| 国家云 | Azure AD 门户终结点 |
|----------------|--------------------------|
| 适用于美国政府的 Azure AD | `https://portal.azure.us` |
| Azure AD 德国 | `https://portal.microsoftazure.de` |
| 由世纪互联运营的 Azure AD 中国 | `https://portal.azure.cn` |
| Azure AD（全局服务） |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 身份验证终结点

所有各国云在每个环境中分别对用户进行身份验证，并具有单独的身份验证终结点。

下表列出了用于获取令牌的每个国家/地区云的 Azure AD 终结点的基 Url。

| 国家云 | Azure AD 身份验证终结点 |
|----------------|-------------------------|
| 适用于美国政府的 Azure AD | `https://login.microsoftonline.us` |
| Azure AD 德国| `https://login.microsoftonline.de` |
| 由世纪互联运营的 Azure AD 中国 | `https://login.chinacloudapi.cn` |
| Azure AD（全局服务）| `https://login.microsoftonline.com` |

通过使用相应的特定于区域的基 URL，您可以构成 Azure AD 授权或令牌终结点的请求。 例如，对于 Azure 德国：

  - 授权常用终结点为 `https://login.microsoftonline.de/common/oauth2/authorize`。
  - 令牌常用终结点为 `https://login.microsoftonline.de/common/oauth2/token`。

对于单租户应用程序，将为"common"，在你的租户 ID 或名称与以前的 Url。 例如 `https://login.microsoftonline.de/contoso.com`。

> [!NOTE]
> [Azure AD v2.0 授权]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview)和令牌终结点都仅适用于全局服务。 它们不被支持的国家/地区云部署。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

若要了解如何在一个国家/地区云环境中调用 Microsoft Graph Api，请转到[国家/地区云部署中的 Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments)。

> [!IMPORTANT]
> 某些服务和全局服务的特定区域中的功能可能不可用在所有国家/地区云。 若要找出哪些服务是可用，请转到[区域的可用产品](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)。

若要了解如何通过使用 Microsoft 标识平台构建的应用程序，请按照[Microsoft 身份验证库 (MSAL) 教程](msal-national-cloud.md)。 具体而言，此应用将在用户登录并获取访问令牌调用 Microsoft Graph API。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure 中国世纪互联](https://docs.microsoft.com/azure/china/)
- [Azure 德国](https://docs.microsoft.com/azure/germany/)
- [Azure AD 身份验证基础知识](authentication-scenarios.md)
