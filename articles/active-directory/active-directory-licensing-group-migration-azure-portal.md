---
title: "如何在 Azure Active Directory 中将各个经许可的用户迁移到组中 | Microsoft 文档"
description: "如何使用 Azure Active Directory 从单个用户许可证切换到基于组的许可"
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
ms.openlocfilehash: 6b77dd4e9a6d361a05382397e89b575896fdad84
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>如何在 Azure Active Directory 中将经许可的用户添加到组以进行许可

可能已通过“直接分配”将现有的许可证部署到了组织中的用户；也就是说，使用 PowerShell 脚本或其他工具为单个用户分配了许可证。 如果想要开始使用基于组的许可来管理组织中的许可证，需要使用一个迁移计划，将现有解决方案无缝替换为基于组的许可。

要记住的最重要的一件事是，应该避免出现迁移到基于组的许可后，导致用户暂时失去其当前分配的许可证的情况。 应该避免可能导致删除许可证的任何过程，以消除用户失去服务及其数据访问权限的风险。

## <a name="recommended-migration-process"></a>建议的迁移过程

1. 如果正在使用现有的自动化工具（例如 PowerShell）管理和删除用户的许可证分配， 请让它继续按现状运行。

2. 创建新的许可组（或确定要使用哪些现有组），并确保将全部所需的用户添加为成员。

3. 将所需的许可证分配到这些组；目标应该是反映现有自动化工具（例如 PowerShell）向这些用户应用的相同许可状态。

4. 验证是否已向这些组中的所有用户应用许可证。 为此，可以检查每个组的处理状态并查看审核日志。

  - 查看单个用户的许可证详细信息，即可查明该用户的许可状态。 可以看到，他们具有“直接”分配的以及从组“继承”的相同许可证。

  - 可以运行 PowerShell 脚本来[确认许可证是如何分配给用户的](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses)。

  - 如果将一个产品许可证直接分配给用户，同时又通过组将同一个许可证分配给用户，该用户只会使用一个许可证。 因此，不需要使用其他许可证来执行迁移。

5. 在每个组中检查处于错误状态的用户，验证是否没有许可证分配失败。 有关详细信息，请参阅[识别和解决组的许可证问题](active-directory-licensing-group-problem-resolution-azure-portal.md)。

6. 考虑删除原始的直接分配；可以“分阶段”逐步执行该操作，先观察这种操作对一部分用户的影响。

  你可以在用户上，保留原始的直接分配，但在用户离开时它们仍将保留原始许可，它可能是其授权的组不希望所需。

## <a name="an-example"></a>示例

某家组织有 1,000 个用户。 所有用户都需要企业移动性 + 安全性 (EMS) 许可证。 有 200 个用户在财务部工作，他们需要 Office 365 企业版 E3 许可证。 该组织在本地运行一个 PowerShell 脚本，用于添加和删除新进用户与离职用户的许可证。 他们希望将该脚本替换为基于组的许可，让 Azure AD 自动管理许可证。

可能的迁移过程如下：

1. 使用 Azure 门户将 EMS 许可证分配到 Azure AD 中的“所有用户”组。 将 E3 许可证分配到包含全部所需用户的“财务部”组。

2. 确认为每个组的所有用户完成了许可证分配。 转到每个组的边栏选项卡，选择“许可证”，并检查“许可证”边栏选项卡顶部的处理状态。

  - 查看“已向所有用户应用最新的许可证更改”，确认处理已完成。

  - 查看顶部的通知，了解可能未成功为其分配许可证的用户。 某些用户的许可证是否已用完？ 某些用户的许可证 SKU 是否有冲突，从而导致他们无法继承组许可证？

3. 定点检查某些用户，确认是否为他们应用了直接许可证和组许可证。 转到用户的边栏选项卡，选择“许可证”，并检查许可证的状态。

  - 下面是迁移期间预期的用户状态：

      ![预期的用户状态](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  这种状态可以确认用户既有直接许可证，也有继承的许可证。 可以看到同时分配了 **EMS** 和 **E3** 许可证。

  - 选择每个许可证可显示有关已启用的服务的详细信息。 可以使用这些信息来检查直接许可证和组许可证是否为用户启用了完全相同的服务计划。

      ![检查服务计划](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. 确认直接许可证和组许可证相同后，可以开始删除用户的直接许可证。 一开始可以试着在门户中删除单个用户的许可证，并运行自动化脚本批量删除许可证。 下面是通过门户删除同一用户的直接许可证的示例。 请注意，许可证状态将保持不变，但我们不再会看到直接分配。

  ![已删除直接许可证](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>后续步骤

若要详细了解通过组进行许可证管理的其他方案，请阅读

* [将许可证分配到 Azure Active Directory 中的组](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory 中基于组的许可是什么？](active-directory-licensing-whatis-azure-portal.md)
* [识别和解决 Azure Active Directory 中组的许可问题](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [基于 Azure Active Directory 组的许可的其他方案](active-directory-licensing-group-advanced.md)
