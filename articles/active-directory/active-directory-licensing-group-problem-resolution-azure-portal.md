---
title: "在 Azure Active Directory 中解决组的许可证问题 | Microsoft 文档"
description: "使用基于 Azure Active Directory 组的许可时，如何识别和解决许可证分配问题"
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
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 955efc9e6b209195935d1f7c13f96c6a42536b2a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/27/2017

---

# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>识别和解决 Azure Active Directory 中组的许可证分配问题

Azure Active Directory (Azure AD) 中基于组的许可引入了处于许可错误状态的用户的概念。 本文说明用户可能会以此状态结束的原因。

如果直接将许可证分配给单个用户而不使用基于组的许可，分配操作可能会失败。 例如，对用户执行 PowerShell cmdlet `Set-MsolUserLicense` 时，由于与业务逻辑相关的多个原因，该 cmdlet 可能会失败。 例如，可能许可证数量不足，或者两个服务计划之间存在冲突，不能同时分配。 将立即向你返回该问题的报告。

使用基于组的许可时，可能会发生相同的错误，但是在 Azure AD 服务分配许可证时在后台发生这些错误。 由于此原因，这些错误无法立即传达给你。 但是，这些错误会记录在用户对象中，并通过管理门户进行报告。 请注意，为用户提供许可证的原始意图永远不会丢失，但以错误状态记录，供以后调查和解决。

## <a name="how-to-find-license-assignment-errors"></a>如何查找许可证分配错误

1. 若要查找特定组中处于错误状态的用户，请打开相应组的边栏选项卡。 如果有任何用户处于错误状态，会在“许可证”下显示通知。

![组，错误通知](media/active-directory-licensing-group-problem-resolution-azure-portal/group-error-notification.png)

2. 单击通知以打开所有受影响的用户列表。 可以分别单击每个用户以查看更多详细信息。

![组，处于错误状态的用户的列表](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-users-with-errors.png)

3. 若要查找包含至少一个错误的所有组，请在“Azure Active Directory”边栏选项卡上，选择“许可证”，然后选择“概览”。 如果有一些组需要关注，则会显示信息框。

![概览，有关处于错误状态的组的信息](media/active-directory-licensing-group-problem-resolution-azure-portal/group-errors-widget.png)

4. 单击该框可查看具有错误的所有组的列表。 可以单击每个组以了解更多详细信息。

![概览，具有错误的组的列表](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-groups-with-errors.png)


下面是每个潜在问题的说明及其解决方法。

## <a name="not-enough-licenses"></a>许可证不足

**问题：**组中指定的某个产品没有足够的可用许可证。 需要为该产品购买更多的许可证，或者释放其他用户或组中未使用的许可证。

若要查看可用的许可证数量，请转到“Azure Active Directory” > “许可证” > “所有产品”。

若要查看哪些用户和组正在使用许可证，请单击某个产品。 在“许可的用户”下面，可以看到直接或者通过一个或多个组向其分配许可证的所有用户。 在“许可的组”下面，可以看到该产品已分配到的所有组。

**PowerShell：**PowerShell cmdlet 将此错误报告为 _CountViolation_。

## <a name="conflicting-service-plans"></a>冲突的服务计划

**问题：**组中指定的某个产品包含的服务计划，与已通过不同的产品分配给用户的另一个服务计划相冲突。 某些服务计划已配置为不能作为另一个相关服务计划分配给相同的用户。

请考虑以下示例。 为某个用户直接分配了 Office 365 企业版 **E1** 许可证并启用了所有计划。 该用户已添加到分配有 Office 365 企业版 **E3** 产品的组。 此产品包含的服务计划不能与 E1 中包含的计划重叠，因此，组许可证分配会失败并显示“冲突的服务计划”错误。 在此示例中，冲突的服务计划为：

-   SharePoint Online（计划 2）与 SharePoint Online（计划 1）冲突。
-   Exchange Online（计划 2）与 Exchange Online（计划 1）冲突。

若要解决此冲突，需要对直接分配给用户的 E1 许可证禁用这两个计划。 或者，需要修改整个组许可证分配并在 E3 许可证中禁用计划。 或者，你可能会决定删除用户的 E1 许可证（如果 E1 许可证在 E3 许可证的上下文中是多余的）。

有冲突的产品许可证的解决方法始终由管理员决定。 Azure AD 不会自动解决许可证冲突。

**PowerShell：**PowerShell cmdlet 将此错误报告为 _MutuallyExclusiveViolation_。

## <a name="other-products-depend-on-this-license"></a>其他产品依赖于此许可证

**问题：**组中指定的某个产品包含的服务计划必须为另一个产品中的另一个服务计划启用才能正常工作。 当 Azure AD 尝试删除基础服务计划时，将出现此错误。 例如，从组中删除用户时，可能会发生此错误。

若要解决此问题，需确保所需的计划仍通过其他某种方法分配给用户，或者为这些用户禁用了依赖服务。 执行这些操作后，可以正确地删除这些用户的组许可证。

**PowerShell：**PowerShell cmdlet 将此错误报告为 _DependencyViolation_。

## <a name="usage-location-isnt-allowed"></a>不允许的使用位置

**问题：**由于当地法律和法规方面的原因，某些 Microsoft 服务不能在所有位置使用。 必须先为用户指定“使用位置”属性，才能将许可证分配给用户。 可以在 Azure 门户的“用户” > “配置文件” > “设置”部分下执行此操作。

当 Azure AD 尝试向使用位置不受支持的用户分配组许可证时，该操作会失败，并且会记录用户发生的此项错误。

若要解决此问题，请从许可组中删除其位置不受支持的用户。 或者，如果当前使用位置值不代表实际用户位置，可以修改这些值，以便下次可以正常分配许可证（如果新位置受支持）。

**PowerShell：**PowerShell cmdlet 将此错误报告为 _ProhibitedInUsageLocationViolation_。

> [!NOTE]
> 当 Azure AD 分配组许可证时，任何未指定使用位置的用户将继承目录的位置。 建议管理员在使用基于组的许可之前，先为用户设置正确的使用位置值，以符合当地法律和法规。

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>如果组中有多个产品许可证，会发生什么情况？

可将多个产品许可证分配到一个组。 例如，可将 Office 365 企业版 E3 和企业移动性 + 安全性分配到某个组，轻松为用户启用所有包含的服务。

Azure AD 会尝试将该组中指定的所有许可证分配给每个用户。 如果由于业务逻辑问题（例如，所有的许可证数量不足，或者与针对用户启用的其他服务冲突），Azure AD 无法分配某个产品，我们也不会在该组中分配其他许可证。

可以查看无法被分配许可证的用户，并且可以查看哪些产品已受此影响。

## <a name="how-to-manage-licenses-for-products-with-prerequisites"></a>如何为有先决条件的产品管理许可证？

你拥有的某些 Microsoft Online 产品可能是“附加内容”- 必须先为用户或组启用先决服务计划，才能向其分配这些产品。 要使用基于组的许可，系统要求先决条件和附加服务计划存在于同一组中。 这是为了确保添加到组的任何用户都能收到功能齐全的产品。 请考虑以下示例：

Microsoft Workplace Analytics 是一个附加产品。 它包含同名单一服务计划。 必须分配以下任一先决条件，才能将此服务计划分配到用户或组：
- Exchange Online（计划 1）
- 或 Exchange Online（计划 2）

如果尝试将此产品本身分配到组，则门户会返回一个错误，单击错误通知会显示以下详细信息：

![组，缺少先决条件](media/active-directory-licensing-group-problem-resolution-azure-portal/group-prerequisite-required.png)

单击详细信息会显示以下错误消息：

授权操作失败。添加或删除从属服务前，请确保组具有必要的服务。Microsoft Workplace Analytics 服务还要求启用 Exchange Online（计划 2）。**

为将此附加许可分配到组，必须确保该组也包含先决服务计划。 例如，可更新已经包含完整 Office 365 E3 产品的现有组，并向其添加附加产品。

也可创建一个独立的组，其中只包含使附加产品运行所需的最少产品 - 它可用于仅向特定用户授予附加产品的许可证。 在本例中，我们向同一组分配了以下产品：
- Office 365 企业版 E3，仅启用了 Exchange Online（计划 2）服务计划
- Microsoft Workplace Analytics

![组，包含先决条件](media/active-directory-licensing-group-problem-resolution-azure-portal/group-addon-with-prerequisite.png)

从现在起，添加到此组的任何用户都将使用 1 个 E3 产品许可正和 1 个 Workplace Analytics 产品许可证。 同时，这些用户可以是另一组的成员，为其提供完整的 E3 产品，他们仍只使用该产品的 1 个许可证。

> [!TIP]
> 可为每个先决服务计划创建多个组。 例如，如果有用户使用 Office 365 Enterprise E1，也有用户 Office 365 Enterprise E3，则可创建两个组来授权 Microsoft Workplace Analytics：一个使用 E1 作为先决条件，另一个使用 E3****。 这样，不需要额外的许可证，即可将附加内容分发到 E1 和 E 3 用户。

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>对于某个用户，许可证分配在无提示的情况下失败，因为 Exchange Online 中存在重复代理地址

如果使用的是 Exchange Online，可能会使用相同的代理地址值错误地配置租户中的某些用户。 当基于组的许可尝试向这类用户分配许可证时，它会失败，并且不会记录错误（与上述其他错误情况不同）- 这是此功能的预览版本中的限制，我们将在*公开上市*之前解决它。

> [!TIP]
> 如果你注意到某些用户未收到许可证，并且没有针对这些用户错误记录，请先检查它们是否具有重复代理地址。
> 这可以通过对 Exchange Online 执行以下 PowerShell cmdlet 来完成：
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> [这篇文章](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online)包含有关此问题的详细信息，其中包括有关[如何使用远程 PowerShell 连接到 Exchange Online](https://technet.microsoft.com/library/jj984289.aspx) 的信息。

为受影响的用户解决了代理地址问题之后后，请确保强制对组进行许可证处理以确保现在可以再次应用许可证。

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>如何强制处理组中的许可证以解决错误？

根据解决错误时采取的措施，可能需要手动触发组的处理来更新用户状态。

例如，如果通过删除用户的直接许可证分配来释放某些许可证，则需要触发以前无法完全为所有用户成员提供许可证的组的处理。 为此，请找到组边栏选项卡，打开“许可证”，并在工具栏中选择“重新处理”按钮。

## <a name="next-steps"></a>后续步骤

若要详细了解通过组进行许可证管理的其他方案，请参阅以下部分：

* [将许可证分配到 Azure Active Directory 中的组](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory 中基于组的许可是什么？](active-directory-licensing-whatis-azure-portal.md)
* [如何将单个许可用户迁移到 Azure Active Directory 中基于组的许可](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 基于组的许可的其他方案](active-directory-licensing-group-advanced.md)

