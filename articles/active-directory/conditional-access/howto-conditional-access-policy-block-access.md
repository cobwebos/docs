---
title: 条件访问 - 阻止访问 - Azure Active Directory
description: 创建自定义条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68ab302e5c24835174d03b3579c9ec587bfd9006
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601582"
---
# <a name="conditional-access-block-access"></a>条件访问：阻止访问

对于采用保守云迁移方法的组织，可以使用“全部阻止”策略。 

> [!CAUTION]
> 阻止策略配置不当可能会导致组织被锁定，无法使用 Azure 门户。

这样的策略可能会产生意外的副作用。 在启用之前，正确的测试和验证至关重要。 在进行更改时，管理员应使用条件访问 [仅报告模式](concept-conditional-access-report-only.md) 和 [What If 工具](what-if-tool.md) 等工具进行更改。

## <a name="user-exclusions"></a>排除用户

条件访问策略是强大的工具，建议从策略中排除以下帐户：

* 紧急访问帐户或不受限帐户，用于防止租户范围的帐户锁定   。 在极少数情况下，所有管理员都被锁定在租户之外，此时可以使用紧急访问管理帐户登录到租户，采取相关步骤来恢复访问权限。
   * 有关详细信息，可参阅[管理 Azure AD 中的紧急访问帐户](../users-groups-roles/directory-emergency-access.md)一文。
* **服务帐户**和**服务主体**，例如 Azure AD Connect 同步帐户。 服务帐户是非交互性帐户，不绑定到任何特定用户。 它们通常由允许对应用程序进行编程访问的后端服务使用，但也用于出于管理目的登录到系统。 应该排除这样的服务帐户，因为无法以编程方式完成 MFA。 服务主体进行的调用不被条件访问阻止。
   * 如果组织在脚本或代码中使用这些帐户，请考虑将其替换为[托管标识](../managed-identities-azure-resources/overview.md)。 作为临时解决方法，可以从基线策略中排除这些特定的帐户。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

如果用户不在受信任的网络中，以下步骤将帮助创建条件访问策略来阻止访问除 [Office 365](concept-conditional-access-cloud-apps.md#office-365-preview) 之外的所有应用。 将这些策略设置为 [仅限报表模式](howto-conditional-access-insights-reporting.md) ，以便管理员可以确定他们将对现有用户产生的影响。 当管理员确认策略按预期方式应用时，他们可以打开这些策略的开关  。

第一个策略阻止访问除 Microsoft 365 应用程序之外的所有应用（如果不在受信任位置上）。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组”  。
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下  选择“用户和组”  ，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在“云应用或操作”  下，选择以下选项：
   1. 在“包括”下，选择“所有云应用”。  
   1. 在“排除”  下，依次选择“Office 365 (预览版)”  、“选择”  、“完成”  。
1. 在“条件”  下，执行以下操作：
   1. 在“条件”   >   “位置”下，
      1. 将“配置”设置为“是”  
      1. 在“包括”下，选择“任何位置”。  
      1. 在“排除”  下，选择“所有受信任位置”  。
      1. 选择“完成”  。
   1. 在“客户端应用(预览版)”  下，将“配置”设置为“是”，选择“完成”，然后再选择“完成”。    
1. 在“访问控制” > “授权”下，选择“阻止访问”，然后选择“选择”。    
1. 确认设置，然后将“启用策略”设置为“仅限报告”。  
1. 选择“创建”  ，以便创建启用策略所需的项目。

下面创建了第二个策略来要求 Microsoft 365 的用户使用多重身份验证或兼容的设备。

1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组”  。
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下  选择“用户和组”  ，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，依次选择“选择应用”、“Office 365 (预览版)”、“选择”、“完成”。    
1. 在“访问控制”   >   “授权”下，选择“授权访问”。 
   1. 选中“需要多重身份验证”  和“要求将设备标记为合规”  ，然后选择“选择”。 
   1. 确保选中“需要所有已选控件”  。
   1. 选择“选择”  。
1. 确认设置，然后将“启用策略”设置为“仅限报告”。  
1. 选择“创建”  ，以便创建启用策略所需的项目。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-insights-reporting.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
