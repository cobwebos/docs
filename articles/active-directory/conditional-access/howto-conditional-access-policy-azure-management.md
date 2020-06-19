---
title: 条件访问 - 要求将 MFA 用于 Azure 管理 - Azure Active Directory
description: 创建自定义条件访问策略，以要求对 Azure 管理任务进行多重身份验证
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
ms.openlocfilehash: 09d07717895c6faa506d692becb261e834e44890
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995344"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>条件访问：要求将 MFA 用于 Azure 管理

组织使用各种 Azure 服务，并通过基于 Azure 资源管理器的工具（如下所示）管理它们：

* Azure 门户
* Azure PowerShell
* Azure CLI

这些工具可以提供对资源的高特权访问，这可以更改订阅范围配置、服务设置和订阅计费。 为了保护这些特权资源，Microsoft 建议要求对访问这些资源的任何用户进行多重身份验证。

## <a name="user-exclusions"></a>用户排除项

条件访问策略是功能强大的工具，建议从策略中排除以下帐户：

* 紧急访问或不受限帐户，用于防止租户范围帐户锁定。 在极少数情况下，所有管理员都被锁定在租户外，紧急访问管理帐户可用于登录租户，以采取措施来恢复访问。
   * 有关详细信息，请参阅文章[在 Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)。
* 服务帐户和服务主体，例如 Azure AD Connect 同步帐户。 服务帐户是不与任何特定用户关联的非交互式帐户。 它们通常由后端服务使用，以便可以对应用程序进行编程访问，不过也会用于登录系统以进行管理。 应该排除这类服务帐户，因为无法以编程方式完成 MFA。 条件访问不会阻止由服务主体进行的调用。
   * 如果组织在脚本或代码中使用这些帐户，请考虑将它们替换为[托管标识](../managed-identities-azure-resources/overview.md)。 作为临时解决方法，可以从基线策略中排除这些特定帐户。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤可帮助创建条件访问策略，以要求有权访问 [Microsoft Azure 管理](concept-conditional-access-cloud-apps.md#microsoft-azure-management)应用的用户执行多重身份验证。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 Azure 门户。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织创建一个有意义的策略名称标准。
1. 在“分配”下，选择“用户和组”
   1. 在“包括”下，选择“所有用户”。
   1. 在“排除”下，选择“用户和组”，并选择组织的紧急访问或不受限帐户。 
   1. 选择“完成”。
1. 在“云应用或操作” > “包括”下，依次选择“选择应用”、“Microsoft Azure 管理”、“选择”和“完成”。
1. 在“条件” > “客户端应用(预览)”下，将“配置”设置为“是”，然后选择“完成”。
1. 在“访问控制” > “授予”下，依次选择“"授予访问权限”、“需要多重身份验证”和“选择”。
1. 确认设置并将“启用策略”设置为“开启”。
1. 选择“创建”以创建并启用策略。

## <a name="next-steps"></a>后续步骤

[常用条件访问策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-report-only.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
