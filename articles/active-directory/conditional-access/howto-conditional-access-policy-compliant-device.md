---
title: 条件性访问-需要相容设备-Azure Active Directory
description: 创建自定义条件访问策略以要求符合的设备
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
ms.openlocfilehash: da1a5f54e5e989f661770d518a6753b831b59bd4
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990515"
---
# <a name="conditional-access-require-compliant-devices"></a>条件性访问:要求符合的设备

已部署 Microsoft Intune 的组织可以使用从其设备返回的信息来识别满足符合性要求的设备, 如:

* 需要 PIN 才能解锁
* 需要设备加密
* 要求最小或最大操作系统版本
* 要求设备未越狱或取得 root 权限

此策略符合性信息将转发到 Azure AD 其中条件访问可以决定是授予还是阻止对资源的访问。

## <a name="create-a-conditional-access-policy"></a>创建条件性访问策略

以下步骤将帮助创建条件性访问策略, 以要求将访问资源的设备标记为符合组织的 Intune 合规性策略。

1. 以全局管理员、安全管理员或条件访问管理员身份登录到 **Azure 门户** 。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织为其策略名称创建有意义的标准。
1. 在 "**分配**" 下, 选择 "**用户和组**"
   1. 在 "**包括**" 下, 选择 "**所有用户**"。
   1. 在 "**排除**" 下, 选择 "**用户和组**", 然后选择你的组织的紧急访问帐户或破解玻璃帐户。 
   1. 选择“完成”。
1. 在 "**云应用" 或 "操作** > **包括**" 下, 选择 "**所有云应用**"。
   1. 如果你必须从策略中排除特定的应用程序, 可以从 "**选择排除的云应用**" 下的 "**排除**" 选项卡中进行选择, 然后选择 "**选择**"。
   1. 选择“完成”。
1. 在 "**访问控制** > " "**授权**" 下, 选择 "**要求设备标记为符合**"。
   1. 选择“选择”。
1. 确认设置并将 "**启用策略**" 设置为 **"开"** 。
1. 选择 "**创建**" 以启用策略。

## <a name="next-steps"></a>后续步骤

[条件访问公用策略](concept-conditional-access-policy-common.md)

[使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[设备合规性策略适用于 Azure AD](https://docs.microsoft.com/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
