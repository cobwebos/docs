---
title: "如何为自定义开发的应用程序更改令牌生存期默认设置 |Microsoft 文档"
description: "如何为在 Azure AD 上开发的应用程序更新令牌生存期策略"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: a28eacd820ed28a6470992ce86b060e886c00bcb
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>如何为自定义开发的应用程序更改令牌生存期默认设置

Azure AD Premium 允许应用程序开发人员和租户管理员配置为非机密客户端颁发的令牌的生存期。 根据租户范围或要访问的资源设置令牌生存期策略。

 * 若要设置令牌生存期策略，需要下载 [Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureADPreview)。

 * 运行 **Connect-AzureAD -Confirm** 命令。

 * 以下是一个策略示例，该策略设置最大期限单因素刷新令牌。 创建策略：```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * 若要了解如何创建其他自定义，请查看[配置令牌生存期](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)文档。

## <a name="next-steps"></a>后续步骤
[配置令牌生存期](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Azure AD 令牌参考](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)

