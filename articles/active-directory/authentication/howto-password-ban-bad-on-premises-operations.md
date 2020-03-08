---
title: 启用本地 Azure AD 密码保护
description: 了解如何为本地 Active Directory 域服务环境启用 Azure AD 密码保护
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
ms.openlocfilehash: 4d4c0b0c6ec68c818f107c7e3e3241e4acc0edb7
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671702"
---
# <a name="enable-on-premises-azure-active-password-protection"></a>启用本地 Azure Active Password Protection

用户经常会创建密码，使用常用的词语，如学校、体育团队或名人。 这些密码易于猜测，并使基于字典的攻击变弱。 若要在组织中强制实施强密码，Azure Active Directory （Azure AD）密码保护提供了全局和自定义的禁止密码列表。 如果这些禁止密码列表中存在匹配项，则密码更改请求失败。

若要保护本地 Active Directory 域服务（AD DS）环境，可以安装并配置 Azure AD 密码保护，以便使用本地 DC。 本文说明如何为本地环境启用 Azure AD 密码保护。

有关 Azure AD 密码保护在本地环境中的工作原理的详细信息，请参阅[如何为 Windows Server Active Directory 强制执行 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)。

## <a name="before-you-begin"></a>开始之前

本文说明如何为本地环境启用 Azure AD 密码保护。 在完成本文之前，请在本地 AD DS 环境中[安装并注册 Azure AD 密码保护代理服务和 DC 代理](howto-password-ban-bad-on-premises-deploy.md)。

## <a name="enable-on-premises-password-protection"></a>启用本地密码保护

1. 登录到[Azure 门户](https://portal.azure.com)并浏览到**Azure Active Directory** > **安全** > **身份验证方法** > **密码保护**。
1. 将**Windows Server Active Directory 上的 "启用密码保护**" 选项设置为 *"是"* 。

    如果此设置设置为 "*否*"，则所有部署 Azure AD 密码保护 DC 代理将进入静态模式，其中所有密码都按原样接受。 不执行任何验证活动，并且不生成审核事件。

1. 建议最初将**模式**设置为 "*审核*"。 在你熟悉此功能以及对你组织中的用户产生的影响后，可以将**模式**切换为 "*强制*"。 有关详细信息，请参阅以下有关[操作模式](#modes-of-operation)的部分。
1. 准备就绪后，选择“保存”。

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>操作模式

启用本地 Azure AD 密码保护时，可以使用*审核*模式或*强制*模式。 建议最初的部署和测试始终在审核模式下启动。 然后，应监视事件日志中的条目，以预见启用 "*强制*" 模式后是否会干扰任何现有操作进程。

### <a name="audit-mode"></a>审核模式

*审核*模式旨在作为在 "假设" 模式下运行软件的方法。 每个 Azure AD 密码保护 DC 代理服务根据当前的活动策略评估传入密码。

如果当前策略配置为处于审核模式，则 "错误" 密码将导致事件日志消息，但会进行处理和更新。 此行为是 "审核" 和 "强制" 模式之间的唯一差别。 所有其他操作都运行相同的。

### <a name="enforced-mode"></a>强制模式

*强制*模式旨在作为最终配置。 与处于审核模式时一样，每个 Azure AD 密码保护 DC 代理服务根据当前的活动策略评估传入密码。 不过，当启用强制模式时，将拒绝被视为不安全的密码。

Azure AD 密码保护 DC 代理在强制执行模式下拒绝密码时，最终用户会看到类似的错误，类似于传统的本地密码复杂性强制是否拒绝了密码。 例如，用户可能会在 Windows 登录或更改密码屏幕上看到以下传统错误消息：

*"无法更新密码。为新密码提供的值不满足域的长度、复杂性或历史记录要求。 "*

此消息仅仅是多种可能结果的一个示例。 具体的错误消息可能会有所不同，具体取决于尝试设置不安全密码的实际软件或方案。

受影响的最终用户可能需要与 IT 人员合作，以了解新的要求并选择安全密码。

> [!NOTE]
> Azure AD 密码保护不会控制拒绝弱密码时客户端计算机所显示的特定错误消息。

## <a name="next-steps"></a>后续步骤

若要为你的组织自定义禁止的密码列表，请参阅[配置 Azure AD 密码保护自定义禁止密码列表](tutorial-configure-custom-password-protection.md)。

若要监视本地事件，请参阅["监视本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-monitor.md)"。
