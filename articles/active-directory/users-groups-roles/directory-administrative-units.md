---
title: 管理单元管理（预览版）- Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中使用管理单元获得更精细的委派权限
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77f1a6e5b1e8191c1497e437cc26e1caf1255ba7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472360"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Azure Active Directory 中的管理单元管理（公共预览版）

本文介绍管理单元，这是一个新的 Azure Active Directory (Azure AD) 资源容器，可用于向部分用户委托管理权限，以及向部分用户应用策略。 在 Azure Active Directory 中，利用管理单元，中心管理员能够将权限委派给区域管理员或以粒度级别设置策略。

这在具有独立部门的组织中非常有用，例如：由互相独立的许多自治学校（商学院和工程学校等）组成的大型大学。 此类部门具有其自己的 IT 管理员，这些管理员会控制访问、管理用户并专门针对其部门设置策略。 中心管理员希望能够通过其特定部门中的用户授予这些部门管理员权限。 更具体地说，通过使用此示例，例如，中心管理员可以创建特定学校（商学院）的管理单元并仅使用商学院用户填充该单元。 然后，中心管理员可以将商学院的 IT 员工添加到限定范围的角色中，换而言之，只通过商学院管理单元为 IT 员工授予商学院管理权限。

> [!IMPORTANT]
> 若要使用管理单元，需要管理单元范围的管理员为管理单元中的所有用户提供 Azure Active Directory Premium 许可证和 Azure Active Directory Basic 许可证。 有关详细信息，请参阅 [Azure AD Premium 入门](../fundamentals/active-directory-get-started-premium.md)。
>


从中央管理员的角度来看，管理单元是可以创建并填充资源的目录对象。 **在此预览版中，这些资源只能是用户。** 一旦创建并填充，管理单元即可作为只通过管理单元中所含资源限制授予的权限的范围。

## <a name="managing-administrative-units"></a>管理管理单元
在此预览版中，可以使用 Windows PowerShell cmdlet 的 Azure Active Directory 模块创建和管理管理单元。 若要深入了解如何执行此操作，请参阅[使用管理单元](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

有关软件要求和安装 Azure AD 模块的详细信息，以及有关用于管理管理单元的 Azure AD 模块 cmdlet 的信息（包括语法、参数说明和示例），请参阅 [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

## <a name="next-steps"></a>后续步骤
[Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)
