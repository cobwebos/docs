---
title: 管理对应用程序的同意和评估同意请求 - Azure AD
description: 了解如何在禁用或限制用户同意时管理同意请求，以及如何评估租户范围管理员同意应用程序的请求。
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367850"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>管理对申请的同意和评估同意请求

Microsoft[建议](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations)禁用最终用户对应用程序的同意。 这将将决策过程集中到组织的安全和身份管理员团队。

在禁用或限制最终用户同意后，有几个重要注意事项可确保您的组织保持安全，同时仍允许使用关键业务应用程序。 这些步骤对于最大限度地减少对组织的支持团队和 IT 管理员的影响至关重要，同时防止在第三方应用程序中使用非托管帐户。

## <a name="process-changes-and-education"></a>流程变更和教育

 1. 请考虑启用[管理员同意工作流（预览），](configure-admin-consent-workflow.md)以允许用户直接从同意屏幕请求管理员批准。

 2. 确保所有管理员了解[权限和同意框架](../develop/consent-framework.md)、[同意提示](../develop/application-consent-experience.md)如何工作，以及如何[评估租户范围管理员同意的请求](#evaluating-a-request-for-tenant-wide-admin-consent)。
 3. 查看组织的现有流程，以便用户请求管理员批准应用程序，并在必要时进行更新。 如果进程发生更改：
    * 更新相关文档、监视、自动化等。
    * 将流程更改传达给所有受影响的用户、开发人员、支持团队和 IT 管理员。

## <a name="auditing-and-monitoring"></a>审核和监视

1. [审核应用中并授予组织中的权限](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions)，以确保以前没有授予任何不必要的或可疑的应用程序对数据的访问。

2. 查看[Office 365 中的"检测和补救非法同意授予"，](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)以针对请求 OAuth 同意的可疑应用程序的其他最佳做法和保障措施。

3. 如果您的组织具有适当的许可证：

    * 在[微软云应用程序安全中使用其他 OAuth 应用程序审核功能](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)。
    * 使用[Azure 监视器工作簿监视权限和同意](../reports-monitoring/howto-use-azure-monitor-workbooks.md)相关活动。 "*同意见解"* 工作簿按失败的同意请求数量提供应用视图。 这有助于确定管理员查看和决定是否授予其管理员同意的应用程序的优先级。

### <a name="additional-considerations-for-reducing-friction"></a>减少摩擦的其他注意事项

为了尽量减少对已使用的业务关键型应用程序的影响，请考虑主动向具有大量用户同意授予的应用程序授予管理员同意：

1. 根据登录日志或同意授予活动，对已添加到组织中的使用率高的应用进行盘点。 PowerShell[脚本](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09)可用于快速轻松地发现具有大量用户同意授予的应用程序。

2. 评估尚未获得管理员同意的顶级应用程序。

   > [!IMPORTANT]
   > 在授予租户范围的管理同意之前，请仔细评估应用程序，即使组织中的许多用户已自行同意。

3. 对于已批准的每个应用程序，请使用下面记录的方法之一授予租户范围的管理同意。

4. 对于每个已批准的应用程序，请考虑[限制用户访问](configure-user-consent.md)。

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>评估租户范围的管理同意请求

授予租户范围的管理同意是一项敏感的操作。  权限将代表整个组织授予，并可以包括尝试高特权操作的权限。 例如，角色管理、对所有邮箱或所有站点的完全访问权限以及完全用户模拟。

在授予租户范围的管理同意之前，必须确保信任应用程序和应用程序发布者，以便获得您授予的访问级别。 如果您不确定您了解谁控制了应用程序以及应用程序请求权限的原因，*请不要授予同意*。

以下列表提供了一些建议，在评估授予管理员同意的请求时需要考虑。

* **了解 Microsoft 标识平台中[的权限和同意框架](../develop/consent-framework.md)。**

* **了解[委派权限和应用程序权限](../develop/v2-permissions-and-consent.md#permission-types)之间的区别。**

   应用程序权限允许应用程序访问整个组织的数据，而无需任何用户交互。 委派的权限允许应用程序代表在某个时候登录到应用程序的用户执行操作。

* **了解请求的权限。**

   应用程序请求的权限列在[同意提示](../develop/application-consent-experience.md)中。 展开权限标题将显示权限的说明。 应用程序权限的描述通常以"没有登录用户"结尾。 委派权限的说明通常以"代表登录用户"结尾。 在 [Microsoft 图形权限参考] 中描述了 Microsoft 图形 API 的权限 - 请参阅其他 API 的文档以了解它们公开的权限。

   如果您不理解请求的权限，*请不要同意*。

* **了解请求权限的应用程序以及谁发布了该应用程序。**

   警惕试图看起来像其他应用程序的恶意应用程序。

   如果您怀疑申请或其发布者的合法性，*请不要同意*。 而是寻求其他确认（例如，直接从应用程序发布者）。

* **确保请求的权限与应用程序所需的功能保持一致。**

   例如，提供 SharePoint 站点管理的应用程序可能需要委派访问权限才能读取所有网站集，但不一定需要对所有邮箱的完全访问权限或目录中的完整模拟权限。

   如果您怀疑应用程序请求的权限超过其需要的权限，*请不要授予同意*。 请与应用程序发布者联系以获取更多详细信息。

## <a name="granting-consent-as-an-administrator"></a>授予管理员同意

### <a name="granting-tenant-wide-admin-consent"></a>授予租户范围的管理同意

有关从 Azure 门户、使用 Azure AD PowerShell 或同意提示器本身授予租户范围管理员同意的分步说明，请参阅[授予租户范围管理员同意](grant-admin-consent.md)。

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>代表特定用户授予同意

管理员还可以使用[Microsft 图形 API](https://docs.microsoft.com/graph/use-the-api)代表单个用户授予委派权限的同意，而不是授予整个组织的同意。 有关详细信息，请参阅[代表用户获取访问权限](https://docs.microsoft.com/graph/auth-v2-user)。

## <a name="limiting-user-access-to-applications"></a>限制用户访问应用程序

即使已授予租户范围的管理同意，用户对应用程序的访问权限仍可能受到限制。 有关如何要求用户分配到应用程序的详细信息，请参阅[分配用户和组的方法](methods-for-assigning-users-and-groups.md)。

有关更广泛的概述，包括如何处理其他复杂方案，请参阅使用 Azure [AD 进行应用程序访问管理](what-is-access-management.md)。

## <a name="next-steps"></a>后续步骤

[保护标识基础结构的五个步骤](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[配置管理员同意工作流](configure-admin-consent-workflow.md)

[配置最终用户如何同意应用程序](configure-user-consent.md)

[Microsoft 标识平台中的权限和许可](../develop/active-directory-v2-scopes.md)

[StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)