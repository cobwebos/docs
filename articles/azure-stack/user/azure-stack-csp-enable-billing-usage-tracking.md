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
ms.openlocfilehash: 26ba68be6d4932da77befaf7c968525393c0a033
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>允许云服务提供商管理 Azure Stack 订阅

*适用于：Azure Stack 集成系统*

如果你通过云服务提供商 (CSP) 使用 Azure Stack，则你对 Azure 订阅和 Azure Stack 中资源的访问可能由云服务提供商管理。 或者，你可以管理自己的订阅。 本文探讨如何允许服务提供商代表你访问你的订阅，或确保服务提供商可以管理你的服务。

> [!Note]  
>  如果跳过以下步骤，且 CSP 尚未管理你的帐户，则 CSP 将不能代表你管理 Azure Stack 订阅。

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>使用云服务提供商管理订阅

1. 将 CSP 作为具有用户角色的来宾用户添加到你的租户目录。  有关添加用户的步骤，请参阅[将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. 然后，CSP 将为你创建本地 Azure Stack 订阅。
3. 你就可以开始使用 Azure Stack。
3. 然后，CSP 应在你的订阅中创建资源，以确认他们还可以管理你的资源。 例如，他们可以[使用 Azure Stack 门户创建 Windows 虚拟机](azure-stack-quick-windows-portal.md)。

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>允许云服务提供商使用 RBAC 权限管理你的订阅

将 CSP 作为所有者添加到你的订阅。 

1. 将 CSP 作为具有所有者角色 的来宾用户添加你的租户目录。  有关添加用户的步骤，请参阅[将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. 将所有者角色添加到 CSP 来宾用户。 有关将 CSP 用户添加到订阅的步骤，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. 然后，CSP 将为你创建本地 Azure Stack 订阅。
4. 你就可以开始使用 Azure Stack。
5. 然后，CSP 应在你的订阅中创建资源，以确认他们可以管理你的资源。 

## <a name="next-steps"></a>后续步骤

  - 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](../azure-stack-billing-and-chargeback.md)。
