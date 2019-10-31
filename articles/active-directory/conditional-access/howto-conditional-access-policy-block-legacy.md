---
title: 条件性访问-阻止旧身份验证-Azure Active Directory
description: 创建自定义条件访问策略以阻止旧身份验证协议
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
ms.openlocfilehash: b992973beb7cb132075e47e104733d812dc06ca0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151085"
---
# <a name="conditional-access-block-legacy-authentication"></a>条件性访问：阻止旧身份验证

由于与旧身份验证协议关联的风险增加，Microsoft 建议组织阻止使用这些协议的身份验证请求，并要求新式身份验证。

## <a name="create-a-conditional-access-policy"></a>创建条件性访问策略

以下步骤将帮助创建条件性访问策略来阻止旧式身份验证请求。

1. 以全局管理员、安全管理员或条件访问管理员身份登录到**Azure 门户**。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织为其策略名称创建有意义的标准。
1. 在 "**分配**" 下，选择 "**用户和组**"
   1. 在 "**包括**" 下，选择 "**所有用户**"。
   1. 在 "**排除**" 下，选择 "**用户和组**"，然后选择必须保留使用旧身份验证的功能的任何帐户。 
   1. 选择“完成”。
1. 在 "**云应用" 或 "操作** > **包括**" 下，选择 "**所有云应用**"。
   1. 如果你必须从策略中排除特定的应用程序，可以从 "**选择排除的云应用**" 下的 "**排除**" 选项卡中进行选择，然后选择 "**选择**"。
   1. 选择“完成”。
1. 在 "**条件**" > **客户端应用（预览）** "下，将**配置**设置为 **" 是 "** 。
   1. 仅选中 "**移动应用和桌面客户端** > **其他客户端**" 框。
   2. 选择“完成”。
1. 在 "**访问控制**" > **Grant**"下，选择"**阻止访问**"。
   1. 选择“选择”。
1. 确认设置并将 "**启用策略**" 设置为 **"开"** 。
1. 选择 "**创建**" 以启用策略。

## <a name="next-steps"></a>后续步骤

[条件访问公用策略](concept-conditional-access-policy-common.md)

[使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
