---

title: "Azure Active Directory 中基于组的许可是什么？ | Microsoft Docs"
description: "Azure Active Directory 基于组的许可说明、其工作原理、如何入门和最佳实践"
services: active-directory
keywords: "Azure AD 许可"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 0591b536a9be901085074f4b49fc65b097382835
ms.lasthandoff: 03/01/2017


---

# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Azure Active Directory 中基于组的许可基础知识

Microsoft 云服务（如 Office 365、企业移动性 + 安全性、Dynamics CRM 及其他类似产品）要求为需要访问这些服务的每个用户分配许可证。 管理员通过 Office 或 Azure 管理门户使用 PowerShell cmdlet 管理许可证。 许可证分配状态存储在 Azure Active Directory 这一底层基础结构，它支持所有 Microsoft 云服务的身份管理。

到目前为止，只能在单个用户级别分配许可证，这可能让我们的客户难以进行大规模管理。 例如，若要根据组织变化（例如用户加入或离开组织或部门）添加或删除用户许可证，管理员通常必须编写复杂的 PowerShell 脚本以对云服务进行单独调用。

为了解决这些难题，我们在 Azure AD 许可证管理系统中推出了一项新功能：基于组的许可。 现在可以将一个或多个产品许可证分配给一个组。 Azure AD 会确保将许可证分配给该组的所有成员。 将向加入该组的任何新成员分配适当许可证，并在这些成员离开组时删除相应许可证。 这样就不再需要通过 PowerShell 自动执行许可证管理以反映每个用户的组织和部门结构变化。

## <a name="features"></a>功能

基于组的许可功能有以下主要功能：

- 可以将许可证分配到 Azure AD 中的任何安全组。 安全组可以使用 Azure AD Connect 从本地同步、在 Azure AD 中直接创建（此法创建的安全组也称为仅限云的组），或通过 Azure AD“动态组”功能自动创建。

- 将产品许可证分配到组时，管理员可以禁用产品中的一个或多个服务计划。 通常，在组织尚未准备好开始使用产品中包含的服务时进行此操作，例如管理员要将 Office 365 E3 产品分配给某部门，但又要暂时禁用 Yammer Enterprise 服务。

- 支持需要用户级许可的所有 Microsoft 云服务。 这包括所有 Office 365 产品、企业移动性 + 安全性、Dynamics CRM 等。

- 基于组的许可目前仅通过 [Azure 门户](https://portal.azure.com)提供。 主要使用其他管理门户（例如 Office 365 门户）进行用户和组管理的客户可以照常使用，但需要使用 Azure 门户在组级别管理许可证。

- Azure AD 自动管理由组成员身份更改导致的许可证修改。 通常，用户加入或离开组时，将在成员身份改变后几分钟内修改其许可证。

- 用户可以是指定了许可证策略的多个组的成员；还可以拥有直接分配给任何组外用户的许可证。 生成的用户状态是所有已分配产品和服务许可证的组合。

- 在某些情况下，不能将许可分配给用户；例如当租户中可用许可证不足时，或者同时分配了冲突服务时。 对于 Azure AD 无法为其完全处理组许可证的用户，管理员有权访问其信息，并根据该信息采取纠正措施。

- 在公共预览期间，租户需要 Azure AD 基本版或更高版本的付费或试用订阅才能使用基于组的许可证管理。 此外，从组中继承任何许可证的每个用户都必须分配到付费 Azure AD 版本许可证。

## <a name="next-steps"></a>后续步骤

若要详细了解通过基于组的许可进行许可证管理的其他方案，请阅读

* [将许可证分配到 Azure Active Directory 中的组](active-directory-licensing-group-assignment-azure-portal.md)
* [识别和解决 Azure Active Directory 中组的许可问题](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [如何将单个许可用户迁移到 Azure Active Directory 中基于组的许可](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 基于组的许可的其他方案](active-directory-licensing-group-advanced.md)

