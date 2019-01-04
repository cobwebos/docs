---
title: 什么是基于组的许可 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 基于组的许可，包括其工作原理和最佳做法。
services: active-directory
keywords: Azure AD 许可
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.openlocfilehash: 47d04f6e73d95a7cb1ba63c437b97468041af57f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189857"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Azure Active Directory 中基于组的许可是什么？

Microsoft 付费云服务（如 Office 365、企业移动性 + 安全性、Dynamics 365 及其他类似产品）需要许可证。 这些许可证将分配给需要访问这些服务的每个用户。 若要管理许可证，管理员可以使用某种管理门户（Office 或 Azure）和 PowerShell cmdlet。 Azure Active Directory (Azure AD) 是支持所有 Microsoft 云服务的标识管理的底层基础结构。 Azure AD 存储有关用户许可证分配状态的信息。

到目前为止，只能在单个用户级别分配许可证，因此，大规模管理可能会变得困难。 例如，若要根据组织变化（例如用户加入或离开组织或部门）添加或删除用户许可证，管理员通常必须编写一个复杂的 PowerShell 脚本。 此脚本对云服务进行单独的调用。

为了解决这些难题，Azure AD 现在提供基于组的许可功能。 可将一个或多个产品许可证分配给某个组。 Azure AD 确保将许可证分配给该组的所有成员。 将向加入该组的任何新成员分配适当的许可证。 在这些成员离开组时，将删除相应的许可证。 使用此许可管理功能后，将不再需要通过 PowerShell 自动执行许可证管理以反映每个用户的组织和部门结构变化。

## <a name="licensing-requirements"></a>许可要求
必须具有以下许可证之一才能使用基于组的许可：

- Azure AD Basic 的付费版或试用版订阅

- Office 365 企业版 E3 或 Office 365 A3 及更高版本的付费版或试用版版本

### <a name="required-number-of-licenses"></a>所需许可证数
对于分配了许可证的任何组，你还必须具有用于每个唯一成员的许可证。 虽然不是必须为组的每个成员分配一个许可证，但是你必须至少具有足够的许可证来包括所有成员。 例如，如果你的租户中经许可的组有 1,000 个唯一成员，则必须至少具有 1,000 个许可证才满足许可协议。

## <a name="features"></a>功能

基于组的许可功能提供以下主要功能：

- 可以将许可证分配到 Azure AD 中的任何安全组。 可以使用 Azure AD Connect 从本地同步安全组。 还可以在 Azure AD 中直接创建安全组（也称为仅限云的组），或通过 Azure AD 的动态组功能自动创建安全组。

- 将产品许可证分配到组时，管理员可以禁用产品中的一个或多个服务计划。 通常，在组织尚未准备好开始使用产品中包含的服务时会执行此分配。 例如，管理员可能要将 Office 365 分配给某个部门，但又要暂时禁用 Yammer 服务。

- 支持需要用户级许可的所有 Microsoft 云服务。 此支持包括所有 Office 365 产品、企业移动性 + 安全性和 Dynamics 365。

- 基于组的许可目前仅通过 [Azure 门户](https://portal.azure.com)提供。 如果主要使用其他管理门户（例如 Office 365 门户）来管理用户和组，可以继续这样做。 但是，应该使用 Azure 门户在组级别管理许可证。

- Azure AD 会自动管理由组成员身份更改导致的许可证修改。 通常，在组成员身份发生更改时，许可证修改在几分钟内就会生效。

- 用户可以是指定了许可证策略的多个组的成员。 用户也可以拥有在任何组外部直接分配的许可证。 生成的用户状态是所有已分配产品和服务许可证的组合。 如果为用户分配了来自多个源的同一许可证，则该许可证将仅使用一次。

- 在某些情况下，无法向用户分配许可证。 例如，当租户中可用许可证不足时，或者同时分配了冲突服务时。 对于 Azure AD 无法为其完全处理组许可证的用户，管理员有权访问其信息。 然后，可以根据这些信息采取纠正措施。

## <a name="your-feedback-is-welcome"></a>我们欢迎反馈！

如果有反馈或功能请求，请使用 [Azure AD 管理员论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510)将其与我们共享。

## <a name="next-steps"></a>后续步骤

若要详细了解通过基于组的许可进行许可证管理的其他方案，请参阅：

* [将许可证分配到 Azure Active Directory 中的组](../users-groups-roles/licensing-groups-assign.md)
* [识别和解决 Azure Active Directory 中组的许可问题](../users-groups-roles/licensing-groups-resolve-problems.md)
* [如何将单个许可用户迁移到 Azure Active Directory 中基于组的许可](../users-groups-roles/licensing-groups-migrate-users.md)
* [如何在 Azure Active Directory 中使用基于组的许可在产品许可证之间迁移用户](../users-groups-roles/licensing-groups-change-licenses.md)
* [Azure Active Directory 基于组的许可的其他方案](../users-groups-roles/licensing-group-advanced.md)
* [Azure Active Directory 中基于组的许可的 PowerShell 示例](../users-groups-roles/licensing-ps-examples.md)
