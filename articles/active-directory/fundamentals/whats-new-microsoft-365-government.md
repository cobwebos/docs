---
title: Microsoft 365 政府中的 Azure AD 有哪些新增功能？ | Microsoft Docs
description: 了解 Microsoft 365 政府云实例中对 Azure 活动目录 （Azure AD） 的一些更改，这些更改可能会影响您。
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
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425538"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 政府中的 Azure 活动目录有哪些新增功能

我们在 Microsoft 365 政府云实例中对 Azure 活动目录 （Azure AD） 进行了一些更改，适用于使用以下服务的客户：

- Microsoft Azure 政府

- 微软 365 政府 – 海湾合作委员会高

- 微软 365 政府 + 国防部

本文不适用于 Microsoft 365 政府 – GCC 客户。

## <a name="changes-to-the-initial-domain-name"></a>对初始域名的更改

在组织首次注册 Microsoft 365 政府在线服务期间，系统要求您选择组织的域名`<your-domain-name>.onmicrosoft.com`。 如果您已经有一个带有 .com 后缀的域名，则不会更改任何内容。

但是，如果您注册了新的 Microsoft 365 政府服务，系统将要求您使用`.us`后缀选择域名。 因此，这将是`<your-domain-name>.onmicrosoft.us`。

>[!Note]
>此更改不适用于由云服务提供商 （CSP） 管理的任何客户。

## <a name="changes-to-portal-access"></a>对门户访问的更改

我们更新了 Microsoft Azure 政府、微软 365 政府 + GCC 高和 Microsoft 365 政府 + DoD 的门户终结点，如[端点映射表中](#endpoint-mapping)所示。

以前，客户可以使用全球 Azure （portal.azure.com） 和 Office 365 （portal.office.com） 门户登录。 使用此更新，客户现在必须使用特定的 Microsoft Azure 政府、微软 365 政府 - GCC High 和 Microsoft 365 政府 - DoD 门户登录。

## <a name="endpoint-mapping"></a>终结点映射

下表显示了所有客户的终结点：

| “属性” | 端点详细信息 |
|------|------------------|
| 门户 |微软 Azure 政府：https://portal.azure.us<p>微软 365 政府 – GCC 高：https://portal.office365.us<p>微软 365 政府 + 国防部：https://portal.apps.mil |
| Azure 活动目录权限终结点 | https://login.microsoftonline.us |
| 微软图形API为微软365政府 - GCC高 | https://graph.microsoft.us |
| 微软图形 API 为微软 365 政府 - DoD | https://dod-graph.microsoft.us |
| Azure 政府服务终结点 | 有关详细信息，请参阅[Azure 政府开发人员指南](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| 微软 365 政府 - GCC 高端端点 | 有关详细信息，请参阅[Office 365](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints)美国政府 GCC 高端终结点 |
| 微软 365 政府 - 国防部 | 有关详细信息，请参阅[Office 365](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints)美国政府 DoD 终结点 |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [什么是 Azure 政府版？](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Azure 政府 AAD 授权终结点更新](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [美国政府云中的微软图形终结点](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [办公室 365 美国政府海湾合作委员会高和国防部](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
