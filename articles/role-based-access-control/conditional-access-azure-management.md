---
title: 管理对 Azure 管理 Azure Active Directory 中条件性访问的访问
description: 了解如何在 Azure AD 中使用条件性访问管理对 Azure 的管理访问权限。
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 4ee58835a5055f5382cefa3a049424ffe97a01c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122946"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>管理对 Azure 管理使用条件性访问的访问权限

Azure Active Directory (Azure AD) 中的条件性访问控制云应用基于你指定的特定条件的访问权限。 若要允许访问，您可以创建允许或阻止访问基于满足策略中的要求的条件性访问策略。 

通常情况下，使用条件访问来控制对你的云应用的访问。 此外，还可以设置策略来基于某些条件（如登录风险、位置或设备）控制对 Azure 管理的访问权限并强制实施多重身份验证等要求。

若要创建用于 Azure 管理的策略，请在选择要应用该策略的应用时，选择“云应用”  下的“Microsoft Azure 管理”  。

![Azure 管理的条件性访问](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

创建的策略适用于所有 Azure 管理终结点，包括 Azure 门户、Azure 资源管理器提供程序、经典服务管理 API、Azure PowerShell 和 Visual Studio 订阅管理员门户。 请注意，该策略适用于调用 Azure 资源管理器 API 的 Azure PowerShell。 它不适用于调用 Microsoft Graph 的 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)。

> [!CAUTION]
> 请确保你了解条件性访问策略设置来管理对 Azure 管理的访问之前的工作原理。 请确保不创建可能会阻止自己访问门户的条件。

有关如何设置和使用条件性访问的详细信息，请参阅[Azure Active Directory 中条件性访问](../active-directory/active-directory-conditional-access-azure-portal.md)。