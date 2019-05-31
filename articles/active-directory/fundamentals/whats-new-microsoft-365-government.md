---
title: 什么是 Azure Active Directory，Microsoft 365 政府版的 Azure Active Directory 中的新增功能 |Microsoft Docs
description: 了解如何向 Azure Active Directory (Azure AD) 中 Microsoft 365 政府版云实例，这可能会影响你的某些更改。
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258889"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>什么是 Azure Active Directory，Microsoft 365 政府版中的新增功能

Microsoft 365 政府版云实例，这适用于使用以下服务的客户中，我们已向 Azure Active Directory (Azure AD) 做出一些更改：

- Microsoft Azure 政府

- Microsoft 365 政府版 GCC 高

- Microsoft 365 政府版 DoD

本文不能应用于 Microsoft 365 政府版 gcc 高级版的客户。

## <a name="changes-to-the-initial-domain-name"></a>更改到的初始域名

在你的组织最初注册为 Microsoft 365 政府版联机服务，您需要选择组织的域名， `<your-domain-name>.onmicrosoft.com`。 如果已具有.com 后缀的域名，将更改执行任何操作。

但是，如果您要为新的 Microsoft 365 政府版服务注册后，你将需要选择域名称使用`.us`后缀。 因此，它将为`<your-domain-name>.onmicrosoft.us`。

>[!Note]
>此更改不能应用于任何云服务提供商 (Csp) 托管的客户。

## <a name="changes-to-portal-access"></a>对门户访问权限的更改

我们已更新 Microsoft Azure 政府版、 Microsoft 365 政府版 Gcc，和 Microsoft 365 政府版 DoD、 门户终结点，如中所示[终结点映射表](#endpoint-mapping)。

以前的客户无法登录使用全球 Azure (portal.azure.com) 和 Office 365 (portal.office.com) 门户。 利用此更新，客户必须现在登录中使用的特定 Microsoft Azure 政府版，Microsoft 365 政府版-Gcc 和 Microsoft 365 政府版-DoD 门户。

## <a name="endpoint-mapping"></a>终结点映射

下表显示了为所有客户的终结点：

| 名称 | 终结点详细信息 |
|------|------------------|
| 门户 |Microsoft Azure 政府版： https://portal.azure.us<p>Microsoft 365 政府版 GCC 高： https://portal.office365.us<p>Microsoft 365 政府版 DoD: https://portal.apps.mil |
| Azure Active Directory 颁发机构终结点 | https://login.microsoftonline.us |
| Azure Active Directory 图形 API | https://graph.windows.net |
| 适用于 Microsoft 365 政府-gcc 高级版的 Microsoft Graph API 高 | https://graph.microsoft.us |
| 适用于 Microsoft 365 政府-DoD Microsoft 图形 API | https://dod-graph.microsoft.us |
| Azure 政府版服务终结点 | 有关详细信息，请参阅[Azure Government 开发人员指南](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 政府版-Gcc 终结点 | 有关详细信息，请参阅[Office 365 美国政府 Gcc 终结点](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 政府版-DoD | 有关详细信息，请参阅[Office 365 美国政府版 DoD 终结点](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [什么是 Azure 政府版？](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [更新 azure 政府版 AAD 颁发机构终结点](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [美国政府版云中的 Microsoft Graph 终结点](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 美国政府 GCC High 和 DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)