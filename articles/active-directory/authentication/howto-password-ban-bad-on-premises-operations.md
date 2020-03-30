---
title: 启用本地 Azure AD 密码保护
description: 了解如何为本地活动目录域服务环境启用 Azure AD 密码保护
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00a8b84477226b68913f95c5121bbbdfc2eb09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263811"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>启用本地 Azure 活动目录密码保护

用户通常创建使用常见本地单词（如学校、运动队或名人）的密码。 这些密码很容易猜测，并且对基于字典的攻击很弱。 要在组织中强制实施强密码，Azure 活动目录 （Azure AD） 密码保护提供了全局和自定义禁止的密码列表。 如果这些禁用密码列表中有匹配项，则密码更改请求将失败。

为了保护本地活动目录域服务 （AD DS） 环境，可以安装和配置 Azure AD 密码保护以使用上置目录 DC。 本文介绍如何为本地环境启用 Azure AD 密码保护。

有关 Azure AD 密码保护在本地环境中的工作方式的详细信息，请参阅[如何为 Windows 服务器活动目录强制实施 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)。

## <a name="before-you-begin"></a>开始之前

本文介绍如何为本地环境启用 Azure AD 密码保护。 在完成本文之前，请在本地 AD DS 环境中[安装和注册 Azure AD 密码保护代理服务和 DC 代理](howto-password-ban-bad-on-premises-deploy.md)。

## <a name="enable-on-premises-password-protection"></a>启用本地密码保护

1. 登录到 Azure[门户](https://portal.azure.com)并浏览到**Azure 活动目录** > **安全** > **身份验证方法** > **密码保护**。
1. 将 Windows**服务器活动目录中启用密码保护**的选项设置为 *"是*"。

    当此设置设置为 *"否*"时，所有已部署的 Azure AD 密码保护 DC 代理进入静止模式，所有密码均接受原样。 不执行任何验证活动，也不会生成审核事件。

1. 建议最初将**模式**设置为*审核*。 熟悉该功能及其对组织中用户的影响后，可以将**模式**切换到*强制*。 有关详细信息，请参阅以下有关[操作模式的部分](#modes-of-operation)。
1. 准备就绪后，选择“保存”。****

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>操作模式

启用本地 Azure AD 密码保护时，可以使用*审核*模式或*强制*模式。 我们建议初始部署和测试始终以审核模式开始。 然后，应监视事件日志中的条目，以预测启用*强制*模式后，是否存在任何现有操作进程是否会受到干扰。

### <a name="audit-mode"></a>审核模式

*审核*模式旨在作为在"如果"模式下运行软件的一种方式。 每个 Azure AD 密码保护 DC 代理服务都根据当前活动策略评估传入密码。

如果当前策略配置为处于审核模式，"坏"密码会导致事件日志消息，但会处理和更新。 此行为是审核和强制模式之间的唯一区别。 所有其他操作都运行相同。

### <a name="enforced-mode"></a>强制模式

*强制*模式旨在作为最终配置。 与审核模式一样，每个 Azure AD 密码保护 DC 代理服务根据当前活动策略评估传入密码。 但是，启用强制模式时，根据策略被视为不安全的密码将被拒绝。

当 Azure AD 密码保护 DC 代理在强制模式下拒绝密码时，最终用户将看到类似的错误，就像他们看到其密码是否被传统的本地密码复杂性强制拒绝一样。 例如，用户可能在 Windows 登录或更改密码屏幕看到以下传统错误消息：

*"无法更新密码。为新密码提供的值不符合域的长度、复杂性或历史记录要求。*

此消息仅仅是多种可能结果的一个示例。 特定错误消息可能因尝试设置不安全密码的实际软件或方案而异。

受影响的最终用户可能需要与其 IT 人员合作，以了解新要求并选择安全密码。

> [!NOTE]
> Azure AD 密码保护无法控制客户端计算机在拒绝弱密码时显示的特定错误消息。

## <a name="next-steps"></a>后续步骤

要自定义组织的禁止密码列表，请参阅[配置 Azure AD 密码保护自定义禁止密码列表](tutorial-configure-custom-password-protection.md)。

要监视上前事件，请参阅[监视前 Azure AD 密码保护](howto-password-ban-bad-on-premises-monitor.md)。
