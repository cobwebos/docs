---
title: 条件访问 - 需要通过 MFA 进行 Azure 管理 - Azure Active Directory
description: 创建要求通过多重身份验证来完成 Azure 管理任务的自定义条件访问策略
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
ms.openlocfilehash: 717e81a1385b04d3152beac39105c56754c55c40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049275"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>条件访问：要求将 MFA 用于 Azure 管理

组织使用各种 Azure 服务，并通过基于 Azure 资源管理器的工具（如下所示）管理它们：

* Azure 门户
* Azure PowerShell
* Azure CLI

这些工具可以用来对资源进行特权要求很高的访问，可能会更改订阅范围的配置，服务设置和订阅计费。 为了保护这些特权资源，Microsoft 建议对任何访问这些资源的用户要求多重身份验证。

## <a name="user-exclusions"></a>排除用户

条件访问策略是功能强大的工具，建议从策略中排除以下帐户：

* 紧急访问帐户或不受限帐户，用于防止租户范围的帐户锁定 。 在极少数情况下，所有管理员都被锁定在租户外，紧急访问管理帐户可用于登录租户，以采取措施来恢复访问。
   * 有关详细信息，请参阅文章[在 Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)。
* **服务帐户**和**服务主体**，例如 Azure AD Connect 同步帐户。 服务帐户是不与任何特定用户关联的非交互式帐户。 它们通常由允许对应用程序进行编程访问的后端服务使用，但也用于出于管理目的登录到系统。 应该排除这样的服务帐户，因为无法以编程方式完成 MFA。 服务主体进行的调用不被条件访问阻止。
   * 如果组织在脚本或代码中使用这些帐户，请考虑将它们替换为[托管标识](../managed-identities-azure-resources/overview.md)。 作为临时解决方法，可以从基线策略中排除这些特定帐户。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤可帮助创建条件访问策略，以要求有权访问 [Microsoft Azure 管理](concept-conditional-access-cloud-apps.md#microsoft-azure-management)应用的用户执行多重身份验证。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”  。
1. 选择“新策略”。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”下，选择“用户和组”
   1. 在“包括”下，选择“所有用户”。 
   1. 在“排除”下选择“用户和组”，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成” 。
1. 在“云应用或操作” > “包括”下，依次选择“选择应用”、“Microsoft Azure 管理”、“选择”、“完成”。   
1. 在“条件” > “客户端应用(预览版)”下，在“选择该策略应用到的客户端应用”下保留选择的所有默认值，然后选择“完成”   。
1. 在“访问控制” > “授予”下，依次选择“"授予访问权限”、“需要多重身份验证”和“选择”。
1. 确认设置，然后将“启用策略”设置为“打开”。 
1. 选择“创建”，以便创建启用策略所需的项目。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-insights-reporting.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
