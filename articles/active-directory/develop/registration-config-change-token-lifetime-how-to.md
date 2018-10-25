---
title: 如何为自定义开发的应用程序更改令牌生存期默认设置 |Microsoft 文档
description: 如何为在 Azure AD 上开发的应用程序更新令牌生存期策略
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 4a730c340ea4d1e1137a7449c6d1005ea59917bf
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814002"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>如何为自定义开发的应用程序更改令牌生存期默认设置

Azure AD Premium 允许应用程序开发人员和租户管理员配置为非机密客户端颁发的令牌的生存期。 根据租户范围或要访问的资源设置令牌生存期策略。

1. 若要设置令牌生存期策略，需要下载 [Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureADPreview)。
1. 运行 **Connect-AzureAD -Confirm** 命令。

    以下是一个策略示例，该策略设置最大期限单因素刷新令牌。 创建策略：```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>后续步骤

* 请参阅 [Azure AD 中的可配置令牌生存期](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)，了解如何配置 Azure AD 颁发的令牌生存期，包括如何为组织中的所有应用、多租户应用或组织中的特定服务主体设置令牌生存期。 
* [Azure AD 令牌参考](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

