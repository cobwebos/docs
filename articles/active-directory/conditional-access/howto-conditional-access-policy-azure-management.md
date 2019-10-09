---
title: 条件访问 - 需要通过 MFA 进行 Azure 管理 - Azure Active Directory
description: 创建要求通过多重身份验证来完成 Azure 管理任务的自定义条件访问策略
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
ms.openlocfilehash: dd2259dc715fb54122b721ce40a715c6987947d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170115"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>条件访问：要求将 MFA 用于 Azure 管理

组织会使用各种 Azure 服务，并通过如下所述的基于 Azure 资源管理器的工具对其进行管理：

* Azure 门户
* Azure PowerShell
* Azure CLI

这些工具可以用来对资源进行特权要求很高的访问，可能会更改订阅范围的配置，服务设置和订阅计费。 为了保护这些特权资源，Microsoft 建议对任何访问这些资源的用户要求多重身份验证。

## <a name="user-exclusions"></a>排除用户

条件访问策略是强大的工具，建议从策略中排除以下帐户：

* **紧急访问**或**破解玻璃**帐户，以防止租户范围内的帐户锁定。 在极少数情况下，所有管理员都被锁定在租户之外，此时可以使用紧急访问管理帐户登录到租户，采取相关步骤来恢复访问权限。
   * 有关详细信息，可参阅[管理 Azure AD 中的紧急访问帐户](../users-groups-roles/directory-emergency-access.md)一文。
* **服务帐户**和**服务主体**，例如 Azure AD Connect 同步帐户。 服务帐户是非交互性帐户，不绑定到任何特定用户。 此类帐户通常由后端服务使用，允许以编程方式访问应用程序。 服务帐户应该排除，因为 MFA 不能以编程方式完成。
   * 如果组织在脚本或代码中使用这些帐户，请考虑将其替换为[托管标识](../managed-identities-azure-resources/overview.md)。 作为临时解决方法，可以从基线策略中排除这些特定的帐户。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤将有助于创建条件访问策略，该策略要求那些分配的管理角色执行多重身份验证。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”下，选择“用户和组”
   1. 在“包括”下，选择“所有用户”。
   1. 在“排除”下选择“用户和组”，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”。
1. 在 "**云应用" 或 "操作** > **包括**" 下，选择 "**选择应用**" **，选择 "** **Microsoft Azure 管理**"，然后选择 "**完成**"。
1. 在“访问控制” > “授予”下，依次选择“授予访问权限”、“需要多重身份验证”、“选择”。
1. 确认设置，然后将“启用策略”设置为“打开”。
1. 选择“创建”，以便创建启用策略所需的项目。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
