---
title: 使用 Azure AD 中的条件访问管理对 Azure 管理的访问权限
description: 了解如何使用 Azure AD 中的条件访问管理对 Azure 管理的访问权限。
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
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83758769"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>使用条件访问管理对 Azure 管理的访问权限

> [!CAUTION]
> 在设置策略以管理对 Azure 管理的访问权限之前，请确保了解条件访问的工作原理。 请确保不创建可能会阻止自己访问门户的条件。

Azure Active Directory (Azure AD) 中的条件访问基于你指定的具体条件控制对云应用的访问权限。 若要允许访问，可创建条件访问策略，根据是否满足策略中的要求来允许或阻止访问。 

通常，使用条件访问来控制对云应用的访问权限。 此外，还可以设置策略来基于某些条件（如登录风险、位置或设备）控制对 Azure 管理的访问权限并强制实施多重身份验证等要求。

若要创建用于 Azure 管理的策略，请在选择要应用该策略的应用时，选择“云应用”下的“Microsoft Azure 管理”。

![用于 Azure 管理的条件访问](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

创建的策略适用于所有 Azure 管理终结点，包括以下项：

- Azure 门户
- Azure 资源管理器提供程序
- 经典服务管理 API
- Azure PowerShell
- Visual Studio 订阅管理员门户
- Azure DevOps
- Azure 数据工厂门户

请注意，该策略适用于调用 Azure 资源管理器 API 的 Azure PowerShell。 它不适用于调用 Microsoft Graph 的 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)。

有关如何设置示例策略以便为 Microsoft Azure 管理启用条件访问的详细信息，请参阅文章[条件访问：要求将 MFA 用于 Azure 管理](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md)。
