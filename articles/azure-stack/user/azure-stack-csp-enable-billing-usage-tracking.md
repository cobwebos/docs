---
title: 允许云服务提供商管理 Azure Stack 订阅 | Microsoft Docs
description: 允许云服务提供商访问 Azure Stack 中的订阅。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0cff8f575b87872c0032854f1916b140d7fd62b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>允许云服务提供商管理 Azure Stack 订阅

*适用于：Azure Stack 集成系统*

如果你使用的 Azure 堆栈与云服务提供程序 (CSP)，你可以选择管理你自己的订阅访问资源在 Azure 和 Azure 堆栈中。 你还可以让管理你的订阅为你的提供程序。 本文介绍以下操作：

 * 提供你的服务提供程序访问你的订阅。
 * 请确保服务提供商可以管理你的服务。

> [!Note]
>  如果 CSP 不管理你的帐户，并且你跳过以下步骤，CSP 无法管理你的 Azure 堆栈订阅。

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>使用云服务提供商管理订阅

添加作为 CSP**用户**到你的订阅。

1. 将 CSP 作为具有用户角色的来宾用户添加到你的租户目录。  有关添加用户的步骤，请参阅[将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP 创建你的本地 Azure 堆栈订阅。
3. 你就可以开始使用 Azure Stack。
4. 你的 CSP 应以验证它们还可以管理你的资源在订阅中创建资源。 例如，他们可以[使用 Azure Stack 门户创建 Windows 虚拟机](azure-stack-quick-windows-portal.md)。

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>允许云服务提供商使用 RBAC 权限管理你的订阅

添加作为 CSP**所有者**到你的订阅。

1. 将你的 CSP 作为 guest 用户添加到你租户的目录。  有关添加用户的步骤，请参阅[将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. 将所有者角色添加到 CSP guest 用户。 有关将 CSP 用户添加到订阅的步骤，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. CSP 创建你的本地 Azure 堆栈订阅。
4. 你就可以开始使用 Azure Stack。
5. 你的 CSP 应以验证他们可以管理你的资源在订阅中创建资源。

## <a name="next-steps"></a>后续步骤

若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](../azure-stack-billing-and-chargeback.md)。
