---
title: 管理单元管理（预览）-Azure AD |Microsoft Docs
description: 在 Azure Active Directory 中使用管理单元获得更精细的委派权限
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028414"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory （预览版）中的管理单元管理

本文介绍 Azure Active Directory （Azure AD）中的管理单元。 管理单元是 Azure AD 资源，可以是其他 Azure AD 资源的容器。 在此预览版本中，这些资源只能是用户。 例如，管理单元范围的用户帐户管理员可以更新配置文件信息、重置密码以及仅在其管理单元中为用户分配许可证。

你可以使用管理单元委托用户子集的管理权限，并将策略应用到部分用户。 你可以使用管理单元将权限委派给区域管理员或以粒度级别设置策略。

## <a name="deployment-scenario"></a>部署方案

管理单元可用于具有独立部门的组织。 假设有一家大型大学的示例，此大学由许多自治学校（商业、学校工程等）组成，每个人都有自己的 IT 管理员来控制访问、管理用户和为其学校设置策略。 中心管理员可以为经营者创建一个管理单元，并只使用商业学校学生和员工进行填充。 然后，管理中心可以将商业学校 IT 人员添加到作用域内的角色，该角色只授予商业学校管理单元中 Azure AD 用户的管理权限。

## <a name="license-requirements"></a>许可要求

若要使用管理单元，需要为每个管理单元管理员 Azure Active Directory Premium 许可证。有关详细信息，请参阅[Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)入门。

## <a name="managing-administrative-units"></a>管理管理单元

在此预览版本中，创建和管理管理单元的唯一方式是使用 Windows PowerShell cmdlet 的 Azure Active Directory 模块，如使用[管理单元](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)中所述。

有关软件要求和安装 Azure AD 模块的详细信息，以及有关用于管理管理单元的 Azure AD 模块 cmdlet 的参考信息（包括语法、参数说明和示例），请参阅[Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

## <a name="next-steps"></a>后续步骤

[Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)
