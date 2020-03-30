---
title: 条件访问 - 需要对管理员进行 MFA - Azure Active Directory
description: 创建要求管理员进行多重身份验证的自定义条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c08c8d5d4203ae90cedd826bb5dcb01011d07afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295264"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>条件访问：需要管理员 MFA

分配了管理权限的帐户是攻击者的目标。 要求在这些帐户上进行多重身份验证 (MFA) 后，就可以轻松降低这些帐户受攻击的风险。

Microsoft 建议你至少要求对以下角色进行 MFA：

* 计费管理员
* 条件访问管理员
* Exchange 管理员
* 全局管理员
* 支持（密码）管理员
* 密码管理员
* 安全管理员
* SharePoint 管理员
* 用户管理员

组织可以根据情况选择包括或排除角色。

## <a name="user-exclusions"></a>排除用户

条件访问策略是强大的工具，建议从策略中排除以下帐户：

* 紧急访问帐户或不受限帐户，用于防止租户范围的帐户锁定********。 在极少数情况下，所有管理员都被锁定在租户之外，此时可以使用紧急访问管理帐户登录到租户，采取相关步骤来恢复访问权限。
   * 有关详细信息，可参阅[管理 Azure AD 中的紧急访问帐户](../users-groups-roles/directory-emergency-access.md)一文。
* **服务帐户**和**服务主体**，例如 Azure AD Connect 同步帐户。 服务帐户是非交互性帐户，不绑定到任何特定用户。 此类帐户通常由后端服务使用，允许以编程方式访问应用程序。 应排除服务帐户，因为无法以编程方式完成 MFA。
   * 如果组织在脚本或代码中使用这些帐户，请考虑将其替换为[托管标识](../managed-identities-azure-resources/overview.md)。 作为临时解决方法，可以从基线策略中排除这些特定的帐户。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤将有助于创建条件访问策略，该策略要求那些分配的管理角色执行多重身份验证。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到**Azure 活动目录** > **安全** > **条件访问**。
1. 选择“新策略”****。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在 **"分配"** 下，选择 **"用户"和"组**"
   1. 在“包括”下选择“目录角色(预览)”，******** 然后至少选择以下角色：
      * 身份验证管理员
      * 计费管理员
      * 条件访问管理员
      * Exchange 管理员
      * 全局管理员
      * 支持管理员
      * 密码管理员
      * 安全管理员
      * SharePoint 管理员
      * 用户管理员
   1. 在“排除”下**** 选择“用户和组”****，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择 **"完成**"。
1. 在 **"云应用"或"操作** > **包括**"下，选择"**所有云应用****"，然后选择"完成**"。
1. 在**条件** > **客户端应用（预览）** 下，将 **"配置配置为** **"，** 然后选择 **"完成**"。
1. 在 **"访问控制** > **授予"下**，选择 **"授予访问**"，"**需要多重身份验证**"，然后选择 **"选择**"。
1. 确认设置，然后将“启用策略”设置为“打开”。********
1. 选择“创建”****，以便创建启用策略所需的项目。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用仅条件访问报告模式确定影响](howto-conditional-access-report-only.md)

[使用条件访问"如果"工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
