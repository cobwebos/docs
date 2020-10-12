---
title: 条件访问 - 要求合规设备 - Azure Active Directory
description: 创建要求合规设备的自定义条件访问策略
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
ms.openlocfilehash: c98269f9851272e8caa9b26ae0c57ed13e9a99f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049122"
---
# <a name="conditional-access-require-compliant-devices"></a>条件访问：要求符合的设备

部署了 Microsoft Intune 的组织可以根据从其设备返回的信息来确定符合合规性要求的设备，例如以下要求：

* 要求使用 PIN 来解锁
* 要求设备加密
* 要求操作系统版本有一个下限或上限
* 要求设备未越狱或取得 root 权限

此策略符合性信息会转发给 Azure AD，其中的条件访问可以进行决策，确定是授予还是阻止资源访问权限。 有关设备合规性策略的详细信息，可参阅[使用 Intune 在设备上设置规则以允许访问组织中的资源](/intune/protect/device-compliance-get-started)一文

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤将有助于创建条件访问策略，该策略要求将访问资源的设备标记为符合组织的 Intune 符合性策略。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下  选择“用户和组”  ，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，选择“所有云应用”。 
   1. 如果必须将特定应用程序排除在策略之外，可以在“选择排除的云应用”下的“排除”选项卡中选择它们，然后选择“选择”。   
   1. 选择“完成”  。
1. 在“条件” > “客户端应用(预览版)” > “选择该策略应用到的客户端应用”下保留选择的所有默认值，然后选择“完成”   。
1. 在“访问控制”   >   “授予”下，选择“要求将设备标记为合规”。 
   1. 选择“选择”  。
1. 确认设置，然后将“启用策略”设置为“打开”。  
1. 选择“创建”  ，以便创建启用策略所需的项目。

> [!NOTE]
> 即使你按照上述步骤为“所有用户”  和“所有云应用”  选择“要求将设备标记为合规”  ，也可以将新设备注册到 Intune。 “要求将设备标记为合规”  控制不会阻止 Intune 注册。 

### <a name="known-behavior"></a>已知行为

在 Windows 7、iOS、Android、macOS 和某些第三方 Web 浏览器上，Azure AD 使用客户端证书来标识设备，该证书是在向 Azure AD 注册设备时预配的。 用户首次通过浏览器登录时，系统会提示用户选择此证书。 最终用户必须选择此证书才能继续使用浏览器。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-insights-reporting.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[设备符合性策略与 Azure AD 配合使用](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
