---
title: 条件性访问-需要 MFA 进行 Azure 管理-Azure Active Directory
description: 创建自定义条件访问策略，以要求对 Azure 管理任务进行多重身份验证
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad44cc83d65206ec1f199e57685b0dcc3cbb07a5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151170"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>条件性访问：需要 MFA 进行 Azure 管理

组织使用各种 Azure 服务，并通过 Azure 资源管理器工具（如下所示）对其进行管理：

* Azure 门户
* Azure PowerShell
* Azure CLI

这些工具可以提供对资源的高特权访问权限，从而更改订阅范围的配置、服务设置和订阅计费。 为了保护这些特权资源，Microsoft 建议对访问这些资源的任何用户要求多重身份验证。

## <a name="user-exclusions"></a>用户排除

条件性访问策略是功能强大的工具，我们建议从策略中排除以下帐户：

* **紧急访问**或**破解玻璃**帐户，以防止租户范围内的帐户锁定。 在极少数情况下，所有管理员都被锁定在你的租户中，你的紧急访问管理帐户可用于登录到租户，以采取步骤来恢复访问权限。
   * 有关详细信息，请参阅在[Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)。
* **服务帐户**和**服务原则**，如 Azure AD Connect 同步帐户。 服务帐户是不与任何特定用户关联的非交互式帐户。 它们通常由后端服务使用，并允许对应用程序进行编程访问。 应该排除服务帐户，因为无法以编程方式完成 MFA。
   * 如果你的组织在脚本或代码中使用这些帐户，请考虑将它们替换为[托管标识](../managed-identities-azure-resources/overview.md)。 作为临时解决方法，您可以从基线策略中排除这些特定的帐户。

## <a name="create-a-conditional-access-policy"></a>创建条件性访问策略

以下步骤将帮助创建条件性访问策略，以要求分配的管理角色执行多重身份验证。

1. 以全局管理员、安全管理员或条件访问管理员身份登录到**Azure 门户**。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织为其策略名称创建有意义的标准。
1. 在 "**分配**" 下，选择 "**用户和组**"
   1. 在 "**包括**" 下，选择 "**所有用户**"。
   1. 在 "**排除**" 下，选择 "**用户和组**"，然后选择你的组织的紧急访问帐户或破解玻璃帐户。 
   1. 选择“完成”。
1. 在 "**云应用" 或 "操作** > **包括**" 下，选择 "**选择应用**" **，选择 "** **Microsoft Azure 管理**"，然后选择 "**完成**"。
1. 在 "**访问控制**" > **grant**"下，选择"**授予访问权限**，**需要多重身份验证**"，然后选择"**选择**"。
1. 确认设置并将 "**启用策略**" 设置为 **"开"** 。
1. 选择 "**创建**" 以启用策略。

## <a name="next-steps"></a>后续步骤

[条件访问公用策略](concept-conditional-access-policy-common.md)

[使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
