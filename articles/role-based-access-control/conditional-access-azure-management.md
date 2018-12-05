---
title: 使用 Azure Active Directory 中的条件访问管理对 Azure 管理的访问
description: 了解如何使用 Azure AD 中的条件访问管理对 Azure 管理的访问。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 4cfd3c38631778373e99872fd305d8316cdadb75
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274732"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>使用条件访问管理对 Azure 管理的访问

Azure Active Directory (Azure AD) 中的条件访问基于指定的特定条件控制对云应用的访问权限。 若要允许访问，可创建基于是否满足策略中的要求允许或阻止访问的条件访问策略。 

通常，使用条件访问来控制对云应用的访问权限。 此外，还可以设置策略来基于某些条件（如登录风险、位置或设备）控制对 Azure 管理的访问权限并强制实施多重身份验证等要求。

若要创建用于 Azure 管理的策略，请在选择要应用该策略的应用时，选择“云应用”下的“Microsoft Azure 管理”。

![用于 Azure 管理的条件访问](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

创建的策略适用于所有 Azure 管理终结点，包括经典 Azure 门户、Azure 门户、Azure 资源管理器提供程序、经典服务管理 API 和 Azure PowerShell。 请注意，该策略适用于调用 Azure 资源管理器 API 的 Azure PowerShell。 它不适用于调用 Microsoft Graph 的 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)。

> [!CAUTION]
> 在设置策略以管理对 Azure 管理的访问之前，请确保了解条件访问的工作原理。 请确保不创建可能会阻止自己访问门户的条件。

有关如何设置和使用条件访问的详细信息，请参阅 [Azure Active Directory 中的条件访问](../active-directory/active-directory-conditional-access-azure-portal.md)。