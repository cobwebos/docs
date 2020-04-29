---
title: 条件性访问-阻止访问-Azure Active Directory
description: 创建自定义条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2834fd3d4901b6394eabe000f9efc572c2efd497
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80755081"
---
# <a name="conditional-access-block-access"></a>条件性访问：阻止访问

对于具有保守云迁移方法的组织，可以使用 "阻止所有策略" 选项。 

> [!CAUTION]
> 阻止策略的配置错误可能会导致组织被 Azure 门户锁定。

这样的策略可能会产生意外的副作用。 在启用之前，正确的测试和验证至关重要。 在进行更改时，管理员应使用条件访问[仅报告模式](concept-conditional-access-report-only.md)和[What If 工具](what-if-tool.md)等工具进行更改。

## <a name="user-exclusions"></a>排除用户

条件访问策略是强大的工具，建议从策略中排除以下帐户：

* 紧急访问帐户或不受限帐户，用于防止租户范围的帐户锁定********。 在极少数情况下，所有管理员都被锁定在租户之外，此时可以使用紧急访问管理帐户登录到租户，采取相关步骤来恢复访问权限。
   * 有关详细信息，可参阅[管理 Azure AD 中的紧急访问帐户](../users-groups-roles/directory-emergency-access.md)一文。
* **服务帐户**和**服务主体**，例如 Azure AD Connect 同步帐户。 服务帐户是非交互性帐户，不绑定到任何特定用户。 它们通常由后端服务用于访问应用程序，但也用于登录系统以进行管理。 应该排除这样的服务帐户，因为无法以编程方式完成 MFA。 由服务主体进行的调用未被条件访问阻止。
   * 如果组织在脚本或代码中使用这些帐户，请考虑将其替换为[托管标识](../managed-identities-azure-resources/overview.md)。 作为临时解决方法，可以从基线策略中排除这些特定的帐户。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

如果用户不在受信任的网络中，以下步骤将帮助创建条件性访问策略来阻止除[Office 365](concept-conditional-access-cloud-apps.md#office-365-preview)之外的所有应用程序的访问权限。 将这些策略设置为[仅限报表模式](howto-conditional-access-report-only.md)，以便管理员可以确定他们将对现有用户产生的影响。 当管理员认为策略按预期方式应用时，可以将其切换到 **"打开**"。

第一个策略阻止访问除 Office 365 应用程序之外的所有应用（如果不在受信任位置上）。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到**Azure Active Directory** > **安全** > **条件性访问**。
1. 选择“新策略”****。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组”  。
   1. 在 "**包括**" 下，选择 "**所有用户**"。
   1. 在“排除”下**** 选择“用户和组”****，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在 "**云应用" 或 "操作**" 下，选择以下选项：
   1. 在 "**包括**" 下，选择 "**所有云应用**"。
   1. 在 "**排除**" 下，选择 " **Office 365 （预览版）**"，选择 "**选择**"，然后选择 "**完成**"。
1. **条件**：
   1. **条件** > **位置**。
      1. 将“配置”设置为“是”********
      1. 在 "**包括**" 下，选择**任何位置**。
      1. 在 "**排除**" 下，选择 "**所有受信任位置**"。
      1. 选择“完成”  。
   1. 在 "**客户端应用（预览）**" 下，将 "**配置**" 设置为 **"是"** **，然后选择**"**完成**"。
1. 在 "**访问控制** > "**下，选择**"**阻止访问**"，然后选择 "**选择**"。
1. 确认设置并将 "**启用策略**" 设置为 "**仅报告**"。
1. 选择“创建”****，以便创建启用策略所需的项目。

下面创建了第二个策略，要求为 Office 365 用户提供多重身份验证或兼容的设备。

1. 选择“新策略”****。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组”  。
   1. 在 "**包括**" 下，选择 "**所有用户**"。
   1. 在“排除”下**** 选择“用户和组”****，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在 "**云应用或操作** > **包括**" 下，选择 "**选择应用**"，选择 " **Office 365 （预览版）**"，**然后选择 "****完成**"。
1. 在 "**访问控制** > " "**授权**" 下，选择 "**授予访问权限**"。
   1. 选择 "**需要多重身份验证**并**要求设备标记为符合**" 选择 "**选择**"。
   1. 确保选中 **"要求所有选定的控件"** 。
   1. 选择“选择”  。
1. 确认设置并将 "**启用策略**" 设置为 "**仅报告**"。
1. 选择“创建”****，以便创建启用策略所需的项目。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件性访问仅报告模式来确定影响](howto-conditional-access-report-only.md)

[使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
