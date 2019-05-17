---
title: 如何为自定义开发的应用程序更改令牌生存期默认设置 |Microsoft 文档
description: 如何为在 Azure AD 上开发的应用程序更新令牌生存期策略
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3d11d282a2405d37614bfac41dd3f7ad49353d8
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545529"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>如何为自定义开发的应用程序更改令牌生存期默认设置

本文介绍如何使用 Azure AD PowerShell 设置令牌生存期策略。 Azure AD Premium 允许应用程序开发人员和租户管理员配置为非机密客户端颁发的令牌的生存期。 根据租户范围或要访问的资源设置令牌生存期策略。

1. 若要设置令牌生存期策略，需要下载 [Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureADPreview)。
1. 运行 **Connect-AzureAD -Confirm** 命令。

    以下是一个策略示例，该策略设置最大期限单因素刷新令牌。 创建策略：```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>后续步骤

* 请参阅 [Azure AD 中的可配置令牌生存期](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)，了解如何配置 Azure AD 颁发的令牌生存期，包括如何为组织中的所有应用、多租户应用或组织中的特定服务主体设置令牌生存期。 
* [Azure AD 令牌参考](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

