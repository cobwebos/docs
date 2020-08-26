---
title: Privileged Identity Management 中的 Azure AD 角色功能 |Microsoft Docs
description: 如何在 Privileged Identity Management (PIM) 中管理 Azure AD 角色的分配
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c039842a04923bc02aa288576570d51c39156c40
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2020
ms.locfileid: "88784005"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management 中 Azure AD 角色的管理功能

Privileged Identity Management 中 Azure AD 角色的管理体验已更新，以统一 Azure AD 角色和 Azure 资源角色的管理方式。 之前，Azure 资源角色的 Privileged Identity Management 具有几个 Azure AD 角色无法使用的关键功能。

通过当前推出的更新，我们将两者合并为一个单一的管理体验。在此体验中，你可获得与 Azure 资源角色相同的 Azure AD 角色功能。 本文将介绍已更新的功能和任何要求。

## <a name="time-bound-assignments"></a>时限性分配

以前，角色分配有两种可能的状态： *合格* 和 *永久*。 现在，你还可以为每种类型的分配设置开始时间和结束时间。 此添加操作为您提供了四种可能的状态：

- 永久合格
- 永久活动
- 符合条件，具有指定的开始日期和结束日期进行分配
- 活动，具有指定的开始和结束日期分配

在许多情况下，即使你不希望用户每次都具有合格的分配并激活角色，仍可通过设置分配的过期时间来保护 Azure AD 组织。 例如，如果你有一些符合条件的临时用户，可考虑设置一个过期时间，以便在他们工作完成后自动将他们从角色分配中删除。

## <a name="new-role-settings"></a>新角色设置

我们还为 Azure AD 角色添加了新的设置。

- **以前**，只能按角色配置激活设置。 也就是说，已将诸如多重身份验证要求和事件/请求票证要求等激活设置应用于符合指定角色要求的所有用户。
- **现在**，你可以配置单个用户是否需要执行多重身份验证，然后才能激活角色。 此外，还可以对与特定角色相关的 Privileged Identity Management 电子邮件进行高级控制。

## <a name="extend-and-renew-assignments"></a>延期并续订分配

在理解了有时限的分配后，你可能会问的第一个问题是，角色过期后会出现什么情况？ 在此新版本中，我们针对此情况提供了两个选项：

- **扩展**：当角色分配接近其过期时间时，用户可以使用 Privileged Identity Management 来请求该角色分配的扩展
- **续订**：角色分配过期后，用户可以使用 Privileged Identity Management 来请求续订该角色分配

这两个用户发起的操作都需要全局管理员或特权角色管理员的批准。 管理员将不再需要管理这些过期的分配。 他们只需要等待延期或续订请求，并在请求有效的情况下批准它们即可。

## <a name="api-changes"></a>API 更改

当客户将更新的版本推广到其 Azure AD 组织中时，现有的图形 API 将停止工作。 必须转而使用 [Azure 资源角色的图形 API](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)。 若要使用该 API 管理 Azure AD 角色，请在签名中将 `/azureResources` 与 `/aadroles` 交换，并使用 `resourceId` 的目录 ID。

我们已经尽最大努力与所有使用以前的 API 的客户取得联系，让他们提前知道这项更改。 如果你的 Azure AD 组织已移至新版本，但你仍依赖于旧的 API，请在 pim_preview@microsoft.com 与团队联系。

## <a name="powershell-change"></a>PowerShell 更改

对于使用 Azure AD 角色的 Privileged Identity Management PowerShell 模块的客户，PowerShell 将停止更新。 必须使用 Azure AD 预览 PowerShell 模块中的 Privileged Identity Management cmdlet 来替换以前的 cmdlet。 安装 [PowerShell 库](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)中的 Azure AD PowerShell 模块。 现在，可以[在此 PowerShell 模块中阅读 PIM 操作的文档和示例](powershell-for-azure-ad-roles.md)。

## <a name="next-steps"></a>后续步骤

- [分配 Azure AD 自定义角色](azure-ad-custom-roles-assign.md)
- [删除或更新 Azure AD 自定义角色分配](azure-ad-custom-roles-update-remove.md)
- [配置 Azure AD 自定义角色分配](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定义](../users-groups-roles/directory-assign-admin-roles.md)