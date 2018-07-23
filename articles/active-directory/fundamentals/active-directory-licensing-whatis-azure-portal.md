---
title: Azure AD 中基于组的许可是什么？ | Microsoft Docs
description: Azure Active Directory 基于组的许可、其工作原理和最佳做法的说明
services: active-directory
keywords: Azure AD 许可
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: fundamentals
ms.topic: overview
ms.workload: identity
ms.date: 06/13/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 5f5aab6a8514636e8be09deb773748080871b9d4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005225"
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Azure Active Directory 中基于组的许可基础知识

Microsoft 付费云服务（如 Office 365、企业移动性 + 安全性、Dynamics 365 及其他类似产品）需要许可证。 这些许可证将分配给需要访问这些服务的每个用户。 若要管理许可证，管理员可以使用某种管理门户（Office 或 Azure）和 PowerShell cmdlet。 Azure Active Directory (Azure AD) 是支持所有 Microsoft 云服务的标识管理的底层基础结构。 Azure AD 存储有关用户许可证分配状态的信息。

到目前为止，只能在单个用户级别分配许可证，因此，大规模管理可能会变得困难。 例如，若要根据组织变化（例如用户加入或离开组织或部门）添加或删除用户许可证，管理员通常必须编写一个复杂的 PowerShell 脚本。 此脚本对云服务进行单独的调用。

为了解决这些难题，Azure AD 现在提供基于组的许可功能。 可将一个或多个产品许可证分配给某个组。 Azure AD 确保将许可证分配给该组的所有成员。 将向加入该组的任何新成员分配适当的许可证。 在这些成员离开组时，将删除相应的许可证。 这样就不再需要通过 PowerShell 自动执行许可证管理以反映每个用户的组织和部门结构变化。

>[!Note]
>基于组的许可是 Azure Active Directory (Azure AD) 的一项公共预览版功能，可通过任何付费 Azure AD 许可计划获得。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="features"></a>功能

基于组的许可功能提供以下主要功能：

- 可以将许可证分配到 Azure AD 中的任何安全组。 可以使用 Azure AD Connect 在本地同步安全组。 还可以在 Azure AD 中直接创建安全组（也称为仅限云的组），或通过 Azure AD 的动态组功能自动创建安全组。

- 将产品许可证分配到组时，管理员可以禁用产品中的一个或多个服务计划。 通常，在组织尚未准备好开始使用产品中包含的服务时会执行此操作。 例如，管理员可能要将 Office 365 分配给某个部门，但又要暂时禁用 Yammer 服务。

- 支持需要用户级许可的所有 Microsoft 云服务。 这包括所有 Office 365 产品、企业移动性 + 安全性和 Dynamics 365。

- 基于组的许可目前仅通过 [Azure 门户](https://portal.azure.com)提供。 如果主要使用其他管理门户（例如 Office 365 门户）来管理用户和组，可以继续这样做。 但是，应该使用 Azure 门户在组级别管理许可证。

- Azure AD 会自动管理由组成员身份更改导致的许可证修改。 通常，在组成员身份发生更改时，许可证修改在几分钟内就会生效。

- 用户可以是指定了许可证策略的多个组的成员。 用户也可以拥有在任何组外部直接分配的许可证。 生成的用户状态是所有已分配产品和服务许可证的组合。

- 在某些情况下，无法向用户分配许可证。 例如，当租户中可用许可证不足时，或者同时分配了冲突服务时。 对于 Azure AD 无法为其完全处理组许可证的用户，管理员有权访问其信息。 然后，可以根据这些信息采取纠正措施。

- 在公共预览期，租户需要 Azure AD 基本版或高级版的付费或试用订阅才能使用基于组的许可证管理。

## <a name="your-feedback-is-welcome"></a>我们欢迎反馈！

如果有反馈或功能请求，请使用 [Azure AD 管理员论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510)将其与我们共享。

## <a name="next-steps"></a>后续步骤

若要详细了解通过基于组的许可进行许可证管理的其他方案，请参阅：

* [将许可证分配到 Azure Active Directory 中的组](../users-groups-roles/licensing-groups-assign.md)
* [识别和解决 Azure Active Directory 中组的许可问题](../users-groups-roles/licensing-groups-resolve-problems.md)
* [如何将单个许可用户迁移到 Azure Active Directory 中基于组的许可](../users-groups-roles/licensing-groups-migrate-users.md)
* [Azure Active Directory 基于组的许可的其他方案](../users-groups-roles/licensing-group-advanced.md)
