---
title: Azure AD 密码保护操作和报告-Azure Active Directory
description: Azure AD 的密码保护部署后操作和报告
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5ff7f0bbf1bf474a611ae033165bca6dfaac676
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097632"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Azure AD 密码保护操作规程

在本地完成 [Azure AD 密码保护的安装](howto-password-ban-bad-on-premises-deploy.md)后，必须在 Azure 门户中配置几个项目。

## <a name="configure-the-custom-banned-password-list"></a>配置自定义禁止密码列表

遵循[配置自定义禁止密码列表](howto-password-ban-bad-configure.md)一文中的指导步骤，为组织自定义禁止密码列表。

## <a name="enable-password-protection"></a>启用密码保护

1. 登录到[Azure 门户](https://portal.azure.com)并浏览到**Azure Active Directory**、**身份验证方法**和**密码保护**。
1. 将“对 Windows Server Active Directory 启用密码保护”设置为“是”
1. 如[部署指南](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy)中所述，我们建议最初将“模式”设置为“审核”
   * 在熟悉该功能后，可将“模式”切换为“强制”
1. 单击“保存”

![在 Azure 门户中启用 Azure AD 密码保护组件](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>审核模式

审核模式是在“假设”模式下运行软件的一种方式。 每个 DC 代理服务根据当前处于活动状态的策略评估传入的密码。 如果当前策略配置为“审核”模式，则“错误”的密码会导致生成事件日志消息，但这些密码仍被接受。 这是“审核”与“强制”模式之间的唯一差别；其他所有操作的运行方式相同。

> [!NOTE]
> Microsoft 建议始终以“审核”模式开始进行初始的部署和测试。 然后，应该监视事件日志中的事件，以尝试预测在启用“强制”模式后，任何现有操作过程是否受到干扰。

## <a name="enforce-mode"></a>强制模式

强制模式旨在用作最终配置。 与在上述“审核”模式下一样，每个 DC 代理服务根据当前处于活动状态的策略评估传入的密码。 不过，如果启用了“强制”模式，根据策略视为不安全的密码将被拒绝。

如果在“强制”模式下 Azure AD 密码保护 DC 代理拒绝了某个密码，最终用户可见的影响，等同于传统本地密码复杂性措施拒绝密码时所体现出的影响。 例如，用户在 Windows 登录\更改密码屏幕上可能会看到以下传统错误消息：

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

此消息仅仅是多种可能结果的一个示例。 具体的错误消息根据尝试设置不安全密码的实际软件或场景而异。

受影响的最终用户可能需要咨询其 IT 人员才能了解新的要求和选择安全密码。

> [!NOTE]
> Azure AD 密码保护不会控制拒绝弱密码时客户端计算机所显示的特定错误消息。

## <a name="enable-mode"></a>启用模式

应将此设置保留为其默认启用（是）状态。 将此设置配置为已禁用（“否”）将导致所有已部署的 Azure AD 密码保护 DC 代理进入静止模式，在这种模式下，将按现样接受所有密码，并且不会执行任何验证活动（例如，甚至连审核事件都不会发布）。

## <a name="next-steps"></a>后续步骤

[对 Azure AD 密码保护的监视](howto-password-ban-bad-on-premises-monitor.md)
