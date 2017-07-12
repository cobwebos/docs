---
title: "如何更改自定义开发应用程序的令牌生存期默认值 | Microsoft Docs"
description: "如何更新在 Azure AD 上开发的应用程序的令牌生存期策略"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 54c30123bb582f515dfb0324cdfd897a6c4af5c0
ms.contentlocale: zh-cn
ms.lasthandoff: 04/14/2017

---


<a id="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application" class="xliff"></a>

# 如何更改自定义开发应用程序的令牌生存期默认值

Azure AD Premium 允许应用开发人员和租户管理员配置为非机密客户端所颁发的令牌生存期。 根据租户范围或要访问的资源设置令牌生存期策略。

 * 若要设置令牌生存期策略，需要下载 [Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureADPreview)。

 * 运行 **Connect-AzureAD -Confirm** 命令。

 * 以下是一个设置最大期限单因素刷新令牌的策略示例。 创建策略：```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * 若要了解如何创建其他自定义，请查看[配置令牌生存期](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)文档。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[配置令牌生存期](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Azure AD 令牌参考](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)


