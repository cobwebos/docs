---
title: 将应用标记为“发布者已验证” - Microsoft 标识平台 | Azure
description: 说明如何将应用标记为“发布者已验证”。 当应用程序标记为“发布者已验证”时，表示发布者已使用 Microsoft 合作伙伴网络帐户（该帐户已完成验证过程）验证了身份，并将 MPN 帐户与其应用程序注册相关联。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 0c1f279e49b938fb391223bec47d23326e34b2ea
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595686"
---
# <a name="mark-your-app-as-publisher-verified-preview"></a>将应用标记为“发布者已验证”（预览版）

当应用程序标记为“发布者已验证”时，表示发布者已使用 Microsoft 合作伙伴网络 (MPN) 帐户验证了身份，并将此 MPN 帐户与其应用程序注册相关联。 本文介绍如何完成[发布者验证（预览版）](publisher-verification-overview.md)过程。

## <a name="quickstart"></a>快速入门
如果已注册 Microsoft 合作伙伴网络 (MPN)，并且已经满足[先决条件](publisher-verification-overview.md#requirements)，则可以立即开始： 

1. 导航到预览版[应用注册门户](https://aka.ms/PublisherVerificationPreview)。

1. 选择应用，然后单击“品牌打造”。 

1. 单击“添加 MPN ID 以验证发布者”并查看列出的要求。

1. 输入 MPN ID，然后单击“验证并保存”。

有关特定权益、要求和常见问题的更多详细信息，请参阅[概述](publisher-verification-overview.md)。


## <a name="mark-your-app-as-publisher-verified"></a>将应用标记为“发布者已验证”
确保已满足[先决条件](publisher-verification-overview.md#requirements)，然后按照以下步骤将应用标记为“发布者已验证”。  

1. 确保你已使用组织 (Azure AD) 帐户登录，该帐户已获授权对要标记为“发布者已验证”的应用进行更改，并可对合作伙伴中心的 MPN 帐户进行更改。 

    - 在 Azure AD 中，此用户必须是应用的所有者或拥有以下角色之一：应用程序管理员、云应用程序管理员和全局管理员。 

    - 在合作伙伴中心中，该用户必须拥有以下角色之一：MPN 管理员、帐户管理员或全局管理员（这是 Azure AD 中主导的共享角色）。 

1. 导航到应用注册门户的预览版本：  

1. 单击想要标记为“发布者已验证”的应用，然后打开“品牌打造”边栏选项卡。 

1. 确保已正确设置应用的发布者域。 此域必须为： 

    - 作为经 DNS 验证的自定义域添加到 Azure AD 租户，  

    - 匹配在 MPN 帐户验证过程中使用的电子邮件地址所在的域。 

1. 单击页面底部附近的“添加 MPN 以验证发布者”。 

1. 输入“MPN ID”。 此 MPN ID 必须是： 

    - 已完成验证过程的有效 Microsoft 合作伙伴网络帐户。  

    - 组织的合作伙伴全局帐户 (PGA)。 

1. 单击“验证并保存”。 

1. 等待处理请求，这可能需要几分钟的时间。 

1. 如果验证成功，“发布者验证”窗口将关闭，并将你返回到“品牌打造”边栏选项卡。 你将在经验证的“发布者显示名称”旁边看到一个蓝色的经过验证的徽章。 

1. 在你成功完成这个过程后，得到系统提示同意使用你的应用的用户将很快开始看到徽章，但可能需要一些时间才能在整个系统中复制。 

1. 测试此功能的方法是，登录到你的应用程序并确保同意屏幕上显示有经过验证的徽章。 如果你作为同意使用该应用的用户身份登录，则可以使用 prompt=consent 查询参数来强制执行同意提示。 

1. 如果想让其他任何应用显示此徽章，则根据需要重复此过程。 可以使用 Microsoft Graph 来更快速地批量完成这些工作，PowerShell cmdlet 很快就会推出。 有关详细信息，请参阅[进行 Microsoft API Graph 调用](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)。 

就这么简单！ 如果你对过程、结果或总体功能有任何反馈，请告诉我们。 

## <a name="next-steps"></a>后续步骤
如果遇到问题，请阅读[故障排除信息](troubleshoot-publisher-verification.md)。