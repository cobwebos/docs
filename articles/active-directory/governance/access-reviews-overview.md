---
title: 什么是访问评审？ - Azure Active Directory | Microsoft Docs
description: 使用 Azure Active Directory 访问评审可以控制组成员身份和应用程序访问权限，从而满足组织的监管、风险管理和符合性计划。
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/05/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe8b50775390d60f75fafeae490e0307912600f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125282"
---
# <a name="what-are-azure-ad-access-reviews"></a>Azure AD 访问评审是什么？

Azure Active Directory (Azure AD) 访问评审可以使组织有效地管理组成员身份、对企业应用程序的访问权限，以及角色分配。 可以定期评审用户的访问权限，确保相应人员持续拥有访问权限。

下面的视频简要介绍了访问评审：

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>访问评审为何重要？

Azure AD 支持在组织内进行内部协作和与外部组织的用户（例如合作伙伴）进行协作。 用户可以加入组、邀请来宾、连接到云应用以及通过工作或个人设备远程工作。 自助服务的便捷性使人们需要更好的访问管理功能。

- 新员工加入时，如何确保他们获得有助于提高工作效率的相应访问权限？
- 当员工在团队间调动或离开公司时，如何确保删除旧的访问权限，尤其是在涉及来宾时？
- 访问权限过多可能影响审计结果，导致利益受损，因为此类情况表示对访问权限缺乏控制。
- 需要主动与资源所有者联系，确保他们定期评审有权访问其资源的用户。

## <a name="when-to-use-access-reviews"></a>何时使用访问评审？

- **特权角色用户过多：** 建议检查多少用户具有管理访问权限，其中有多少用户是全局管理员，以及检查是否存在向其分配管理任务后未将其删除的受邀来宾和合作伙伴。 可以重新验证 [Azure AD 角色](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)（例如全局管理员）或 [Azure 资源角色](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)（例如 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 体验中的用户访问权限管理员）中的角色分配用户。
- **自动化不可行：** 可针对安全组或 Office 365 组中的动态成员资格创建规则，但如果人力资源数据不在 Azure AD 中或者如果用户在离开组后仍需访问权限来培训其接任者应该怎么办？ 对于此类情况，可以对该组创建评审，确保仍需访问权限的用户能够继续获得访问权限。
- **将组用于新用途：** 如果要将组同步到 Azure AD，或计划为所有销售团队组成员启用 Salesforce 应用程序，则要求组所有者在将组用于其他风险内容前评审组成员资格会非常有用。
- **业务关键数据访问:** 对于某些资源, 可能需要让他们之外的人员定期注销并提供理由来确定他们出于审核目的需要访问的原因。
- **要维护策略的例外列表：** 在理想情况下，所有用户都会遵循访问策略来保护对组织资源的访问。 但是，有时，某些业务案例要求例外处理。 IT 管理员可以管理此任务、避免忽视策略例外情况，为审核员提供定期评审这些例外情况的证明。
- **要求组所有者确认他们在组中是否仍需要来宾：** 员工访问可能会通过一些本地 IAM 自动执行，但受邀来宾除外。 如果组为来宾授予了业务敏感内容的访问权限，则由组所有者负责确认来宾是否仍有对访问权限的合法业务需求。
- **定期重复评审：** 可以设置按设定频率（例如每周、每月、每季度或每年）定期对用户进行访问评审，审阅者将在每次评审开始前收到通知。 审阅者可以借助友好界面和智能建议的帮助，批准或拒绝访问权限。

## <a name="where-do-you-create-reviews"></a>在哪里创建评审？

可在 Azure AD 访问评审、Azure AD 企业应用（预览版）或 Azure AD PIM 中创建访问评审，具体取决于要评审的内容。

| 用户访问权限 | 审阅者身份 | 评审创建位置 | 审阅者体验 |
| --- | --- | --- | --- |
| 安全组成员</br>Office 组成员 | 指定的审阅者</br>组所有者</br>自我评审 | Azure AD 访问评审</br>Azure AD 组 | 访问面板 |
| 分配联网应用 | 指定的审阅者</br>自我评审 | Azure AD 访问评审</br>Azure AD 企业应用（预览版） | 访问面板 |
| Azure AD 角色 | 指定的审阅者</br>自我评审 | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure 门户 |
| Azure 资源角色 | 指定的审阅者</br>自我评审 | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure 门户 |


## <a name="onboard-access-reviews"></a>载入访问评审

若要进行检查, 请执行以下步骤。

1. 作为全局管理员或用户管理员, 请登录到要在其中使用访问评审的[Azure 门户](https://portal.azure.com)。

1. 在左侧导航中，单击“Azure Active Directory”。

1. 在左侧菜单中, 单击 "**标识调控**"。

1. 单击 "**访问评审**"。
 
    ![访问评审起始页](./media/access-reviews-overview/access-reviews-overview-onboard.png)

1. 在该页上, 单击 "**立即开始**" 按钮。
    
      ![板载访问评审](./media/access-reviews-overview/access-reviews-overview-select-onboard.png)


## <a name="learn-about-access-reviews"></a>了解访问评审

若要了解有关创建和执行访问评审的详细信息，请观看此简短演示：

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

如果已准备好在组织中部署访问评审，请按照视频中的这些步骤来载入访问评审、培训管理员以及创建第一个访问评审！

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>许可要求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="which-users-must-have-licenses"></a>哪些用户必须有许可证？

与访问评审交互的每个用户都必须拥有付费 Azure AD Premium P2 许可证。 示例包括：

- 创建访问评审的管理员
- 执行访问评审的组所有者
- 分配为评审者的用户
- 执行自我评审的用户

还可以要求来宾用户评审自己的访问权限。 对于你为自己组织的用户之一分配的每个付费 Azure AD Premium P2 许可证, 你可以使用 Azure AD 企业到企业 (B2B) 来邀请最多五名来宾用户的外部用户额度。 这些来宾用户也可以使用 Azure AD Premium P2 功能。 有关详细信息, 请参阅[AZURE AD B2B 协作许可指南](../b2b/licensing-guidance.md)。

下面是一些示例方案，可帮助你确定必须拥有的许可证数量。

| 应用场景 | 计算 | 所需许可证数 |
| --- | --- | --- |
| 管理员创建具有 500 个用户的组 A 的访问评审。 分配 3 个组所有者作为评审者。 | 1个许可证, 适用于每个组所有者作为审阅者的管理员和3个许可证。 | 4 |
| 管理员创建具有 500 个用户的组 A 的访问评审。 将其设为自我评审。 | 1个许可证, 适用于每个用户的管理员 + 500 许可证。 | 501 |
| 管理员将创建组 B 的访问评审, 其中包含5个用户和25个来宾用户。 将其设为自我评审。 | 1个许可证, 适用于每个用户的管理员 + 5 个许可证。<br/>（按所需的 1:5 比例包含来宾用户） | 6 |
| 管理员可使用5个用户和108个来宾用户创建组 C 的访问评审。 将其设为自我评审。 | 1个许可证, 适用于每个用户的管理员 + 5 个许可证, 其中每个用户都具有所需的1:5 比, 涵盖所有108来宾用户。<br/>1 + 5 = 6 个许可证, 涵盖 5\*6 = 30 个来宾用户。 对于剩余的 (108-5\*6) = 78 guest 用户, 需要 78/5 = 16 个附加许可证。 因此, 总共需要 6 + 16 = 22 个许可证。 | 22 |

有关如何将许可证分配给用户的信息，请参阅[使用 Azure Active Directory 门户分配或删除许可证](../fundamentals/license-users-groups.md)。

## <a name="next-steps"></a>后续步骤

- [创建组或应用程序的访问评审](create-access-review.md)
- [针对充当 Azure AD 管理角色的用户创建访问评审](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [评审组或应用程序的访问权限](perform-access-review.md)
- [完成组或应用程序的访问评审](complete-access-review.md)
