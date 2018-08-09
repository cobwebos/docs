---
title: 解决现有 Azure AD 域服务托管域的不匹配目录错误 | Microsoft Docs
description: 了解和解决现有 Azure AD 域服务托管域的不匹配目录错误
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: maheshu
ms.openlocfilehash: f791993a95534f66097e3e7e22141bf34e311f21
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504543"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>解决现有 Azure AD 域服务托管域的不匹配目录错误
你有一个现有 Azure AD 域服务托管域。 导航到 Azure 门户并查看托管域时，会看到以下错误消息：

![不匹配目录错误](.\media\getting-started\mismatched-tenant-error.png)

解决错误前，无法管理此托管域。


## <a name="whats-causing-this-error"></a>什么导致了该错误？
如果托管域和在其中启用它的虚拟网络属于两个不同的 Azure AD 租户，将导致该错误。 例如，你有一个名为“contoso.com”的托管域，并已为 Contoso 的 Azure AD 租户启用。 然后，在其中启用该托管域的 Azure 虚拟网络属于 Fabrikam，Fabrikam 是另一个 Azure AD 租户。

新的 Azure 门户（具体指的是 Azure AD 域服务扩展）基于 Azure 资源管理器生成。 在新式 Azure 资源管理器环境中，将强制实施特定限制，以提供更高的安全性和实现对资源更好的基于角色的访问控制 (RBAC)。 为 Azure AD 租户启用 Azure AD 域服务是一项敏感操作，因为可能导致凭据哈希同步到托管域。 此操作要求用户是目录的租户管理员。 此外，还必须具有对在其中启用托管域的虚拟网络的管理权限。 若要使 RBAC 检查持续工作，托管域和虚拟网络应属于相同的 Azure AD 租户。

简言之，无法在属于另一 Azure AD 租户“fabrikam.com”所有的 Azure 订阅的虚拟网络中为 Azure AD 租户“contoso.com”启用托管域。 

有效配置：在此部署方案中，将为 Contoso Azure AD 租户启用 Contoso 托管域。 托管域在属于 Contoso Azure AD 租户所有的 Azure 订阅的虚拟网络中公开。 因此，托管域以及虚拟网络属于相同的 Azure AD 租户。 此配置有效，且完全受支持。

![有效租户配置](./media/getting-started/valid-tenant-config.png)

不匹配的租户配置：在此部署方案中，将为 Contoso Azure AD 租户启用 Contoso 托管域。 然而，托管域在属于 Fabrikam Azure AD 租户所有的 Azure 订阅的虚拟网络中公开。 因此，托管域以及虚拟网络属于两个不同的 Azure AD 租户。 此配置为不匹配的租户配置，且不受支持。 必须将虚拟网络移到与托管域相同的 Azure AD 租户（即 Contoso）。 有关详细信息，请参阅[解决](#resolution)部分。

![不匹配的租户配置](./media/getting-started/mismatched-tenant-config.png)

因此，如果托管域和在其中启用它的虚拟网络属于两个不同的 Azure AD 租户，将出现此错误。

以下规则在 Resource Manager 环境中适用：
- 一个 Azure AD 目录可以包含多个 Azure 订阅。
- 一个 Azure 订阅可以包含虚拟网络等多个资源。
- 为 Azure AD 目录启用单个 Azure AD 域服务托管域。
- 可以在属于相同 Azure AD 租户中的任意 Azure 订阅的虚拟网络上启用 Azure AD 域服务托管域。


## <a name="resolution"></a>解决方法
可通过两个选择来解决不匹配目录错误。 可以：

- 单击“删除”按钮，删除现有托管域。 使用 [Azure 门户](https://portal.azure.com)重新创建，使托管域和在其中可用的虚拟网络均属于 Azure AD 目录。 将以前加入已删除域的所有计算机重新加入新创建的托管域。

- 将包含虚拟网络的 Azure 订阅移到托管域所属的 Azure AD 目录。 按照[将 Azure 订阅所有权转让给其他帐户](../billing/billing-subscription-transfer.md)一文中的步骤进行操作。


## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [故障排除指南 - Azure AD 域服务](active-directory-ds-troubleshooting.md)
