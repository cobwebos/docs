---
title: 条件访问 - 需要对所有用户进行 MFA - Azure Active Directory
description: 创建要求所有用户执行多重身份验证的自定义条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7af1347ac57e1a5c5ae99744924ea04fe9757581
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "83995337"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>条件访问：要求所有用户执行 MFA

正如 Microsoft 标识安全负责人 Alex Weinert 在其博客文章 [Your Pa$$word doesn't matter](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)（《你的密码无关紧要》）中提到的一样：

> 你的密码无关紧要，但 MFA 很重要！ 根据我们的调查，如果使用 MFA，帐户遭到入侵的可能性将降低 99.9% 以上。

本文中的指导有助于你的组织为你的环境创建一个平衡的 MFA 策略。

## <a name="user-exclusions"></a>排除用户

条件访问策略是强大的工具，建议从策略中排除以下帐户：

* 紧急访问帐户或不受限帐户，用于防止租户范围的帐户锁定   。 在极少数情况下，所有管理员都被锁定在租户之外，此时可以使用紧急访问管理帐户登录到租户，采取相关步骤来恢复访问权限。
   * 有关详细信息，可参阅[管理 Azure AD 中的紧急访问帐户](../users-groups-roles/directory-emergency-access.md)一文。
* **服务帐户**和**服务主体**，例如 Azure AD Connect 同步帐户。 服务帐户是非交互性帐户，不绑定到任何特定用户。 它们通常由允许对应用程序进行编程访问的后端服务使用，但也用于出于管理目的登录到系统。 应该排除这样的服务帐户，因为无法以编程方式完成 MFA。 服务主体进行的调用不被条件访问阻止。
   * 如果组织在脚本或代码中使用这些帐户，请考虑将其替换为[托管标识](../managed-identities-azure-resources/overview.md)。 作为临时解决方法，可以从基线策略中排除这些特定的帐户。

## <a name="application-exclusions"></a>应用程序排除

组织可能正在使用许多云应用程序。 并非所有云应用程序都需要同等的安全性。 例如，工资和出勤应用程序可能需要 MFA，但这自助食堂应用程序可能不需要。 管理员可以选择从其策略中排除特定的应用程序。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤将有助于创建条件访问策略，该策略要求所有用户都执行多重身份验证。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下，选择“所有用户”  
   1. 在“排除”下  选择“用户和组”  ，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，选择“所有云应用”。 
   1. 在“排除”  下，选择任何不需要多重身份验证的应用程序。
1. 在 "**条件**" "  >  **客户端应用（预览）**" 下，将 "**配置**为 **"**。 在 **"选择要应用此策略的客户端应用"** 下，选择 "保留所有默认值" 并选择 "**完成**"。
1. 在“访问控制” > “授予”下，依次选择“"授予访问权限”、“需要多重身份验证”和“选择”。
1. 确认设置并将“启用策略”设置为“开启”。
1. 选择“创建”  ，以便创建启用策略所需的项目。

### <a name="named-locations"></a>命名位置

组织可以选择将已知的网络位置（称为“命名位置”  ）纳入其条件访问策略。 这些命名位置可能包含受信任的 IPv4 网络，例如用于主要办公位置的网络。 有关配置命名位置的详细信息，请参阅文章 [Azure Active Directory 条件访问中的位置条件是什么？](location-condition.md)

在上述示例策略中，如果从组织的企业网络访问云应用，组织可以选择不要求进行多重身份验证。 在这种情况下，组织可以将以下配置添加到策略中：

1. 在“分配”下，选择“条件” > “位置”    。
   1. 配置：“是”  。
   1. 包括“任何位置”。
   1. 排除：“所有受信任的位置”  。
   1. 选择“完成”  。
1. 选择“完成”  。
1. 保存策略更改  。

## <a name="next-steps"></a>后续步骤

[常用条件访问策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-report-only.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
