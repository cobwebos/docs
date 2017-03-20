---

title: "在 Azure Active Directory 中解决组的许可证问题 | Microsoft 文档"
description: "如何使用基于 Azure Active Directory 组的许可来识别和解决许可证分配问题"
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
ms.date: 02/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 5d1c24f0423feffcbde0387ebf3f23a32fda50ce
ms.lasthandoff: 03/08/2017


---

# <a name="identifying-and-resolving-license-assignment-problems-when-using-groups-in-azure-active-directory"></a>当在 Azure Active Directory 中使用组时识别并解决许可证分配问题


Azure Active Directory (Azure AD) 中基于组的许可引入了用户处于许可错误状态的概念。 本文说明用户为何会进入这种状态。 如果直接将许可证分配给单个用户而不使用基于组的许可，分配操作可能失败。 例如，当管理员针对某个用户执行 PowerShell cmdlet `Set-MsolUserLicense` 时，该 cmdlet 可能会出于业务逻辑相关的多种原因而失败，例如，许可证数量不足，或者两个无法同时分配的服务计划之间存在冲突。 系统会立即将该问题报告给执行命令的用户。

使用基于组的许可时，可能会发生相同的错误，但这些错误是在 Azure AD 服务分配许可证时在后台发生的；因此，无法立即将这些错误传达给管理员。 这些错误将记录在用户对象中，通过管理门户报告。 为用户提供许可证的原始意图永远不会丢失，但可能记录在错误状态中，供以后调查和解决。

若要查找每个组中处于错误状态的用户，请打开每个组的边栏选项卡。如果有任何用户处于错误状态，“许可证”下面会显示通知。 选择该通知会打开一个列表，其中列出了所有受影响的用户，可以逐个查看这些用户，了解根本问题。 本文将介绍每种潜在问题及其解决方法。

## <a name="not-enough-licenses"></a>许可证不足

组中指定的某个产品没有足够的可用许可证。 需要为该产品购买更多的许可证，或者释放其他用户或组中未使用的许可证。

若要查看可用的许可证数量，请转到“Azure Active Directory”&gt;“许可证”&gt;“所有产品”。

若要查看哪些用户和组正在使用许可证，请单击某个产品。 在“许可的用户”下面，可以看到直接或者通过一个或多个组向其分配了许可证的所有用户。 在“许可的组”下面，可以看到该产品已分配到的所有组。

## <a name="conflicting-service-plans"></a>冲突的服务计划

组中指定的某个产品包含的服务计划，与已通过不同的产品分配给用户的另一个服务计划相冲突。 某些服务计划已配置为不能分配给同一用户作为另一个相关的服务计划。

考虑以下示例：为某个用户直接分配了 Office 365 企业版 **E1** 许可证并启用了所有计划。 该用户已添加到分配有 Office 365 企业版 **E3** 产品的组。 此产品包含的服务计划不能与 E1 中包含的计划重叠，因此，组许可证分配将会失败并出现“冲突的服务计划”错误。 在此示例中，冲突的服务计划为：

-   SharePoint Online（计划 2）与 SharePoint Online（计划 1）冲突

-   Exchange Online（计划 2）与 Exchange Online（计划 1）冲突

若要解决这种冲突，需要禁用直接分配给用户的 E1 许可证中的两个计划，或者修改整个组许可证分配，禁用 E3 许可证中的计划。 或者，如果 E1 许可证在 E3 许可证的上下文中是多余的，则可以删除用户的 E1 许可证。

有冲突的产品许可证的解决方法始终由管理员决定。 Azure AD 不会自动解决许可证冲突。

## <a name="other-products-depend-on-this-license"></a>其他产品依赖于此许可证

组中指定的某个产品包含的服务计划必须为另一个产品中的另一个服务计划启用才能正常工作。 当 Azure AD 尝试删除基础服务计划（例如，由于从组中删除了用户）时，将出现此错误。

若要解决此问题，需确保所需的计划仍已通过其他某种方法分配给用户，或者为这些用户禁用了依赖服务。 只有这样，才能正常删除这些用户的组许可证。

## <a name="usage-location-not-allowed"></a>不允许的使用位置

由于当地法律和法规方面的原因，某些 Microsoft 服务不能在所有位置使用。 在将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。 可在 Azure 门户中的“用户”&gt;“配置文件”&gt;“设置”部分执行此操作。

当 Azure AD 尝试向使用位置不受支持的用户分配组许可证时，该操作将会失败，并且会记录用户发生的此项错误。

若要解决此问题，请从许可组中删除其位置不受支持的用户。 或者，如果当前使用位置值不代表实际用户的位置，可以修改这些值，以便下次可以正常分配许可证（如果新位置受支持）。

> [!NOTE]
> 当 Azure AD 分配组许可证时，任何没有指定使用位置的用户将继承目录的位置。 建议管理员在使用基于组的许可之前，先为用户设置正确的使用位置值，以符合当地法律和法规。 

## <a name="what-happens-when-there-is-more-than-1-product-license-on-a-group"></a>如果组中有多个产品许可证，会发生什么情况？

可将多个产品许可证分配到一个组。 例如，可将 Office 365 企业版 E3 和企业移动性 + 安全性分配到某个组，轻松为用户启用所有包含的服务。

Azure AD 会尝试将该组中指定的所有许可证分配给每个用户。 如果由于业务逻辑问题（例如，许可证数量不足，或者与针对用户启用的其他服务冲突）而无法分配某个产品，我们也不会在该组中分配其他许可证。

可以查看哪些用户的分配失败，检查哪些产品受到这种失败的影响。

## <a name="how-to-force-processing-of-licenses-in-a-group-to-resolve-errors"></a>如何强制处理组中的许可证以解决错误？

根据解决错误时采取的措施，可能需要手动触发组的处理来更新用户状态。

例如，如果通过删除用户的直接许可证分配来释放某些许可证，则需要触发以前无法完全为所有用户成员提供许可证的组的处理。 为此，请找到组边栏选项卡，打开“许可证”，然后在工具栏中选择“重新处理”按钮。


## <a name="next-steps"></a>后续步骤

若要详细了解通过组进行许可证管理的其他方案，请阅读

* [将许可证分配到 Azure Active Directory 中的组](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory 中基于组的许可是什么？](active-directory-licensing-whatis-azure-portal.md)
* [如何将单个许可用户迁移到 Azure Active Directory 中基于组的许可](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 基于组的许可的其他方案](active-directory-licensing-group-advanced.md)

