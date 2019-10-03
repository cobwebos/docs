---
title: 条件性访问-要求对管理员使用 MFA-Azure Active Directory
description: 创建自定义条件访问策略, 以要求管理员执行多重身份验证
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9624a8c25a1e748f5cde8344b0200bb3c82bab88
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576662"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>条件性访问:需要对管理员的 MFA

分配有管理权限的帐户将被攻击者作为目标。 需要对这些帐户进行多重身份验证 (MFA) 是一种降低这些帐户泄露风险的简单方法。

Microsoft 建议你至少需要对以下角色进行 MFA:

* 全局管理员
* SharePoint 管理员
* Exchange 管理员
* 条件访问管理员
* 安全管理员
* 支持人员 (密码) 管理员
* 密码管理员
* 帐务管理员
* 用户管理员

组织可以选择在合适的情况下包括或排除角色。

## <a name="user-exclusions"></a>用户排除

条件性访问策略是功能强大的工具, 我们建议从策略中排除以下帐户:

* **紧急访问**或**破解玻璃**帐户, 以防止租户范围内的帐户锁定。 在极少数情况下, 所有管理员都被锁定在你的租户中, 你的紧急访问管理帐户可用于登录到租户, 以采取步骤来恢复访问权限。
   * 有关详细信息, 请参阅在[Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)。
* **服务帐户**和**服务原则**, 如 Azure AD Connect 同步帐户。 服务帐户是不与任何特定用户关联的非交互式帐户。 它们通常由后端服务使用, 并允许对应用程序进行编程访问。 应该排除服务帐户, 因为无法以编程方式完成 MFA。
   * 如果你的组织在脚本或代码中使用这些帐户, 请考虑将它们替换为 [托管标识](../managed-identities-azure-resources/overview.md)。 作为临时解决方法, 您可以从基线策略中排除这些特定的帐户。

## <a name="create-a-conditional-access-policy"></a>创建条件性访问策略

以下步骤将帮助创建条件性访问策略, 以要求分配的管理角色执行多重身份验证。

1. 以全局管理员、安全管理员或条件访问管理员身份登录到 **Azure 门户** 。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织为其策略名称创建有意义的标准。
1. 在 "**分配**" 下, 选择 "**用户和组**"
   1. 在 "**包括**" 下, 选择 "**目录角色 (预览版)** ", 并至少选择以下角色:
      * 全局管理员
      * SharePoint 管理员
      * Exchange 管理员
      * 条件访问管理员
      * 安全管理员
      * 支持管理员
      * 密码管理员
      * 帐务管理员
      * 用户管理员
   1. 在 "**排除**" 下, 选择 "**用户和组**", 然后选择你的组织的紧急访问帐户或破解玻璃帐户。 
   1. 选择“完成”。
1. 在 "**云应用或操作** > **包括**" 下, 选择 "**所有云应用**", 然后选择 "**完成**"。
1. 在 "**访问控制** > " "**授权**" 下, 选择 "**授予访问权限**,**需要多重身份验证**", 然后选择 "**选择**"。
1. 确认设置并将 "**启用策略**" 设置为 **"开"** 。
1. 选择 "**创建**" 以启用策略。

## <a name="next-steps"></a>后续步骤

[条件访问公用策略](concept-conditional-access-policy-common.md)

[使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
