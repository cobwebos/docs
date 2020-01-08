---
title: 在 Privileged Identity Management （PIM）中管理 Azure AD 角色 |Microsoft Docs
description: 如何管理分配 Privileged Identity Management （PIM） Azure AD 角色
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef2b27e1ca9c1f6f8dfec1bd2bce4ad09c599cae
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480887"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management 中的 Azure AD 角色的管理功能

已更新 Privileged Identity Management 中 Azure AD 角色的管理体验，以统一管理 Azure AD 角色和 Azure 资源角色的方式。 以前，Azure 资源角色 Privileged Identity Management 具有几个不可用于 Azure AD 角色的关键功能。

当前推出更新时，我们将两者合并为单一的管理体验，在其中你可以获得与 Azure 资源角色相同的 Azure AD 角色功能。 本文通知您已更新的功能和任何要求。

## <a name="time-bound-assignments"></a>时间绑定分配

以前在 Azure AD 角色 Privileged Identity Management 中，你熟悉了具有两种可能状态的角色分配：有*资格*且是*永久性*的。 现在，你可以为每种类型的分配设置开始时间和结束时间。 此添加操作为您提供了四种可能的状态，您可以在其中放置赋值：

- 合法
- 永久活动
- 符合条件，具有指定的开始/结束日期分配
- 活动，具有指定的开始/结束日期分配

在许多情况下，即使不希望用户具有符合条件的分配并每次都激活角色，仍可通过设置分配的过期时间来保护 Azure AD 组织。 例如，如果有一些具有资格的临时用户，请考虑设置一个过期时间，以便在工作完成后将其从角色分配中自动删除。

## <a name="new-role-settings"></a>新角色设置

我们还为 Azure AD 角色添加了新设置。 以前，只能按角色配置激活设置。 也就是说，诸如多重身份验证要求和事件/请求票证要求等激活设置已应用到符合指定角色的所有用户。 现在，你可以配置单个用户是否需要执行多重身份验证，然后才能激活角色。 此外，还可以对与特定角色相关的 Privileged Identity Management 电子邮件进行高级控制。

## <a name="extend-and-renew-assignments"></a>扩展并续订分配

一旦知道了时间绑定分配，你可能会问的第一个问题是角色过期后会出现什么情况？ 在这个新版本中，我们为此方案提供了两个选项：

- 扩展–当角色分配接近其过期时间时，用户可以使用 Privileged Identity Management 来请求该角色分配的扩展
- 续订–角色分配过期后，用户可以使用 Privileged Identity Management 来请求续订该角色分配

用户启动的操作都需要全局管理员或特权角色管理员的批准。 管理员不再需要在管理这些过期的业务中。 它们只需等待扩展或续订请求，并在请求有效时批准它们。

## <a name="api-changes"></a>API 更改

当客户将更新的版本推广到 Azure AD 组织中时，现有 graph API 将停止工作。 必须转换才能使用[Azure 资源角色的图形 API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)。 若要使用该 API 管理 Azure AD 角色，请将 `/azureResources` 替换为签名中的 `/aadroles`，并使用 `resourceId`的目录 ID。

我们已尝试好与使用以前 API 的所有客户联系，让他们提前了解这项更改。 如果你的 Azure AD 组织已移动到新版本，但仍依赖于旧的 API，请联系 pim_preview@microsoft.com的团队。

## <a name="powershell-change"></a>PowerShell 更改

对于使用 Azure AD 角色 Privileged Identity Management PowerShell 模块的客户，PowerShell 将停止使用更新。 必须使用 Azure AD 预览版 PowerShell 模块中的 Privileged Identity Management cmdlet 来替换以前的 cmdlet。 从[PowerShell 库](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安装 Azure AD PowerShell 模块。 Cmdlet 在图形 API 之上构建。

## <a name="next-steps"></a>后续步骤

- [分配 Azure AD 自定义角色](azure-ad-custom-roles-assign.md)
- [删除或更新 Azure AD 自定义角色分配](azure-ad-custom-roles-update-remove.md)
- [配置 Azure AD 自定义角色分配](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定义](../users-groups-roles/directory-assign-admin-roles.md)
