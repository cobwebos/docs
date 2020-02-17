---
title: 管理对应用程序的同意并评估同意请求-Azure AD
description: 了解如何在禁用或限制用户同意的情况下管理同意请求，以及如何评估对应用程序的租户范围内管理员许可的请求。
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
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367850"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>管理对应用程序的同意并评估同意请求

Microsoft[建议](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations)禁用应用程序的最终用户许可。 这会将决策过程与组织的安全和身份管理员团队集中起来。

禁用或限制最终用户许可后，需要考虑几个重要的注意事项，以确保你的组织保持安全，同时仍允许使用业务关键应用程序。 这些步骤对将对组织的支持团队和 IT 管理员的影响降至最低至关重要，同时阻止在第三方应用程序中使用非托管帐户。

## <a name="process-changes-and-education"></a>处理更改和教育

 1. 请考虑启用[管理员同意工作流（预览版）](configure-admin-consent-workflow.md) ，以允许用户直接从同意屏幕请求管理员批准。

 2. 确保所有管理员都了解[权限和许可框架](../develop/consent-framework.md)、[许可提示](../develop/application-consent-experience.md)的工作方式，以及如何[评估租户范围内管理员许可的请求](#evaluating-a-request-for-tenant-wide-admin-consent)。
 3. 查看组织的现有进程，让用户请求管理员批准应用程序，并在必要时进行更新。 如果更改了进程：
    * 更新相关文档、监视、自动化等。
    * 向所有受影响的用户、开发人员、支持团队和 IT 管理员传达进程更改。

## <a name="auditing-and-monitoring"></a>审核和监视

1. [审核应用并授予](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions)组织中的权限，以确保以前未向有人或可疑应用程序授予对数据的访问权限。

2. 查看[在 Office 365 中检测和修正违法许可授予](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)以获得其他最佳方案，并防范请求 OAuth 许可的可疑应用程序。

3. 如果你的组织具有适当的许可证：

    * [在 Microsoft Cloud App Security 中使用其他 OAuth 应用程序审核功能](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)。
    * 使用[Azure Monitor 工作簿来监视权限和同意](../reports-monitoring/howto-use-azure-monitor-workbooks.md)相关的活动。 *同意见解*工作簿提供按失败同意请求数显示的应用视图。 这对于确定管理员查看并决定是否向他们授予管理员许可的优先级设置很有用。

### <a name="additional-considerations-for-reducing-friction"></a>减少摩擦的其他注意事项

若要最大程度地减少对已在使用中的受信任关键业务应用程序的影响，请考虑主动向具有大量用户同意授予的应用程序授予管理员许可：

1. 基于登录日志或许可授予活动，对已添加到你的组织中的应用进行清点，使用高使用率。 PowerShell[脚本](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09)可用于快速轻松地发现具有大量用户许可授予的应用程序。

2. 评估尚未获得管理员许可的排名靠前的应用程序。

   > [!IMPORTANT]
   > 在授予租户范围的管理员许可之前，请仔细评估应用程序，即使组织中的许多用户已同意自己。

3. 对于批准的每个应用程序，请使用下面所述的方法之一授予租户范围内的管理员许可。

4. 对于每个批准的应用程序，请考虑[限制用户访问权限](configure-user-consent.md)。

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>评估租户范围内管理员许可的请求

向租户范围内的管理员授予许可是一项敏感的操作。  将代表整个组织授予权限，并可包含尝试高特权操作的权限。 例如，角色管理、对所有邮箱或所有站点的完全访问权限以及完全用户模拟。

在授予租户范围内的管理员许可之前，必须确保你信任该应用程序和应用程序发布者，以获得你要授予的访问级别。 如果你不知道哪些用户控制应用程序以及应用程序请求权限的原因，请不要*授予同意*。

下面的列表提供了一些建议，以便在评估请求以授予管理员许可时进行考虑。

* **了解 Microsoft 标识平台中的[权限和许可框架](../develop/consent-framework.md)。**

* **了解[委派权限和应用程序权限](../develop/v2-permissions-and-consent.md#permission-types)之间的差异。**

   应用程序权限允许应用程序访问整个组织的数据，无需任何用户交互。 委托权限允许应用程序代表某个用户登录到应用程序。

* **了解所请求的权限。**

   [许可提示](../develop/application-consent-experience.md)中列出了应用程序请求的权限。 展开权限标题将显示权限的说明。 应用程序权限的说明通常以 "无登录用户" 的结尾。 委托权限的说明通常以 "代表登录用户身份" 结束。 [Microsoft Graph 权限引用] 中介绍了 Microsoft Graph API 的权限-请参阅其他 Api 的文档，以了解其所公开的权限。

   如果你不了解请求的权限，请不要*授予同意*。

* **了解哪个应用程序正在请求权限和发布该应用程序的人员。**

   防止恶意应用程序尝试看起来像其他应用程序。

   如果您怀疑某个应用程序或其发行者的合法性，请不要*授予同意*。 而是查找其他确认（例如，直接从应用程序发布者处）。

* **确保请求的权限与应用程序所需的功能保持一致。**

   例如，提供 SharePoint 站点管理的应用程序可能需要委派访问权限才能读取所有站点集合，但不一定需要完全访问所有邮箱或目录中的完全模拟权限。

   如果怀疑应用程序请求的权限超过其需求，请不要*授予许可*。 请与应用程序发布者联系以获取更多详细信息。

## <a name="granting-consent-as-an-administrator"></a>以管理员身份授予许可

### <a name="granting-tenant-wide-admin-consent"></a>向租户范围内的管理员授予许可

请参阅[向租户范围内的管理员授予对应用程序的许可](grant-admin-consent.md)，以获取有关从 Azure 门户、使用 Azure AD PowerShell 或同意提示本身授予租户范围管理员许可的分步说明。

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>代表特定用户授予许可

管理员也可以使用[Microsft 图形 API](https://docs.microsoft.com/graph/use-the-api)授予代表单个用户的委派权限，而不是为整个组织授予许可。 有关详细信息，请参阅[代表用户获取访问权限](https://docs.microsoft.com/graph/auth-v2-user)。

## <a name="limiting-user-access-to-applications"></a>限制用户对应用程序的访问

即使授予了租户范围内的管理员许可，用户对应用程序的访问仍可受到限制。 有关如何要求向应用程序分配用户的详细信息，请参阅[分配用户和组的方法](methods-for-assigning-users-and-groups.md)。

有关更广泛的概述，包括如何处理其他复杂方案，请参阅[使用 Azure AD 进行应用程序访问管理](what-is-access-management.md)。

## <a name="next-steps"></a>后续步骤

[保护标识基础结构的五个步骤](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[配置管理员许可工作流](configure-admin-consent-workflow.md)

[配置最终用户同意应用程序的方式](configure-user-consent.md)

[Microsoft 标识平台中的权限和许可](../develop/active-directory-v2-scopes.md)

[StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)