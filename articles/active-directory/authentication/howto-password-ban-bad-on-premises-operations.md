---
title: Azure AD 密码保护预览版中的操作和报告
description: Azure AD 密码保护预览版中的部署后操作和报告
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 8d7002a014fc6cfab1888a6bc97c0f864de1d99d
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080865"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>预览版：Azure AD 密码保护操作规程

|     |
| --- |
| Azure AD 密码保护是 Azure Active Directory 的一项公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

在本地完成 [Azure AD 密码保护的安装](howto-password-ban-bad-on-premises-deploy.md)后，必须在 Azure 门户中配置几个项目。

## <a name="configure-the-custom-banned-password-list"></a>配置自定义禁止密码列表

遵循[配置自定义禁止密码列表](howto-password-ban-bad-configure.md)一文中的指导步骤，为组织自定义禁止密码列表。

## <a name="enable-password-protection"></a>启用密码保护

1. 登录到 [Azure 门户](https://portal.azure.com)，并依次浏览到 **Azure Active Directory**、“身份验证方法”、“密码保护(预览版)”。
1. 将“在 Windows Server Active Directory 中启用密码保护”设置为“是”
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

## <a name="enable-mode"></a>启用模式

此设置通常应当保留其默认的已启用（“是”）状态。 将此设置配置为已禁用（“否”）将导致所有已部署的 Azure AD 密码保护 DC 代理进入静止模式，在这种模式下，将按现样接受所有密码，并且不会执行任何验证活动（例如，甚至连审核事件都不会发布）。

## <a name="usage-reporting"></a>使用情况报告

可以使用 `Get-AzureADPasswordProtectionSummaryReport` cmdlet 生成活动的摘要视图。 此 cmdlet 的示例输出如下所示：

```PowerShell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

可以使用 –Forest、-Domain 和 –DomainController 参数之一来影响 cmdlet 的报告范围。 不指定参数表示使用 –Forest。

> [!NOTE]
> 此 cmdlet 通过打开与每个域控制器的 Powershell 会话进行工作。 若要获得成功，必须在每个域控制器上启用 PowerShell 远程会话支持，并且客户端必须具有足够的权限。 有关 PowerShell 远程会话要求的详细信息，请在 PowerShell 窗口中运行“Get-Help about_Remote_Troubleshooting”。

> [!NOTE]
> 此 cmdlet 的工作方式是远程查询每个 DC 代理服务的管理事件日志。 如果事件日志包含大量事件，此 cmdlet 可能需要很长时间才能完成。 此外，对大型数据集执行批量网络查询可能会影响域控制器的性能。 因此，在生产环境中应慎用此 cmdlet。

## <a name="dc-agent-discovery"></a>DC 代理发现

`Get-AzureADPasswordProtectionDCAgent` cmdlet 可用于显示域或林中运行的各个 DC 代理的基本信息。 从正在运行的 DC 代理服务注册的 serviceConnectionPoint 对象检索此信息。 此 cmdlet 的示例输出如下所示：

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

每个 DC 代理服务大约每隔一小时更新各种属性。 数据仍可能遇到 Active Directory 复制延迟。

使用 –Forest 或 –Domain 参数可以影响 cmdlet 查询的范围。

## <a name="next-steps"></a>后续步骤

[对 Azure AD 密码保护进行故障排除和监视](howto-password-ban-bad-on-premises-troubleshoot.md)
