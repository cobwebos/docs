---
title: 条件访问 - 阻止旧式身份验证 - Azure Active Directory
description: 创建用于阻止旧式身份验证协议的自定义条件访问策略
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
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295216"
---
# <a name="conditional-access-block-legacy-authentication"></a>条件访问：阻止传统身份验证

由于与旧式身份验证协议相关的风险在增加，Microsoft 建议组织阻止使用这些协议进行的身份验证请求，并要求使用新式身份验证。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤将帮助创建条件访问策略来阻止旧式身份验证请求。 此策略被置于[仅限报表模式](howto-conditional-access-report-only.md)，以便管理员可以确定他们将对现有用户产生的影响。 当管理员认为策略按预期方式应用时，可以通过添加特定组并排除其他组来**切换到或**暂存部署。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到**Azure Active Directory** > **安全** > **条件性访问**。
1. 选择“新策略”****。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在 "**分配**" 下，选择 "**用户和组**"
   1. 在 "**包括**" 下，选择 "**所有用户**"。
   1. 在“排除”下**** 选择“用户和组”****，然后选择任何必须保留使用旧式身份验证的功能的帐户。 排除至少一个帐户以阻止你自行锁定。如果不排除任何帐户，则将无法创建此策略。
   1. 选择“完成”  。
1. 在 "**云应用" 或 "操作**" 下，选择 "**所有云应用**"。
   1. 选择“完成”  。
1. 在 "**条件** > " "**客户端应用（预览）**" 下，将 "**配置**为 **"**。
   1. 仅检查 "**移动应用" 和 "桌面客户** > 端**其他客户端**" 框。
   1. 选择“完成”  。
1. 在 "**访问控制** > **" 权限下，选择**"**阻止访问**"。
   1. 选择“选择”  。
1. 确认设置并将 "**启用策略**" 设置为 "**仅报告**"。
1. 选择“创建”****，以便创建启用策略所需的项目。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件性访问仅报告模式来确定影响](howto-conditional-access-report-only.md)

[使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
