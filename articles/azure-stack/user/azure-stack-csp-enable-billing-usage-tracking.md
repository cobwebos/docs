---
title: 允许云服务提供商管理 Azure Stack 订阅 | Microsoft Docs
description: 允许云服务提供商访问 Azure Stack 中的订阅。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: fca08ef1d803c3bd47b0ae925c4dd12255175f2c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630709"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>允许云服务提供商管理 Azure Stack 订阅

*适用于：Azure Stack 集成系统*

如果你通过云服务提供商 (CSP) 使用 Azure Stack，则可以选择管理自己的订阅以访问 Azure 和 Azure Stack 中的资源。 还可以让提供商管理你的订阅。 本文介绍以下操作：

 * 让服务提供商访问你的订阅。
 * 确保服务提供商可以管理你的服务。

> [!Note]
>  如果 CSP 没有管理你的帐户，并且你跳过以下步骤，则 CSP 无法为你管理 Azure Stack 订阅。

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>使用云服务提供商管理订阅

将 CSP 作为**用户**添加到你的订阅。

1. 将 CSP 作为具有用户角色的来宾用户添加到你的租户目录。  有关添加用户的步骤，请参阅[将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP 将为你创建本地 Azure Stack 订阅。
3. 你就可以开始使用 Azure Stack。
4. CSP 应在你的订阅中创建资源，以确认他们还可以管理你的资源。 例如，他们可以[使用 Azure Stack 门户创建 Windows 虚拟机](azure-stack-quick-windows-portal.md)。

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>允许云服务提供商使用 RBAC 权限管理你的订阅

将 CSP 作为**所有者**添加到你的订阅。

1. 将 CSP 作为来宾用户添加到你的租户目录。  有关添加用户的步骤，请参阅[将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. 将“所有者”角色添加到 CSP 来宾用户。 有关将 CSP 用户添加到订阅的步骤，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. CSP 将为你创建本地 Azure Stack 订阅。
4. 你就可以开始使用 Azure Stack。
5. CSP 应在你的订阅中创建资源，以确认他们可以管理你的资源。

## <a name="next-steps"></a>后续步骤

若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](../azure-stack-billing-and-chargeback.md)。
