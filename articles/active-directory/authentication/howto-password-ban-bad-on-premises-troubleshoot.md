---
title: Azure AD 密码保护的 Azure Active Directory 中的故障排除
description: 了解 Azure AD 密码保护故障排除的常见问题
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60414760"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD 密码保护故障排除

部署 Azure AD 密码保护后，可能需要进行故障排除。 本文详细介绍如何帮助你了解一些常见的故障排除步骤。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 代理找不到目录中的代理

此问题的主要症状是 30017 DC 代理管理员事件日志中的事件。

此问题的常见原因是尚未注册代理。 如果已注册代理，有特定 DC 代理能够看到该代理之前可能会由于 AD 复制延迟而一些延迟。

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 代理不能与代理进行通信

此问题的主要症状是 30018 DC 代理管理员事件日志中的事件。 这可能由多种原因造成：

1. DC 代理位于不允许到已注册的代理服务器的网络连接的网络隔离部分。 此问题可能因此是 expected\benign，只要其他 DC 代理可以通信使用代理服务器，以从 Azure，然后将通过复制的 sysvol 共享中的策略文件通过独立的 DC 来获取下载的密码策略。

1. 代理主机正在阻止访问 RPC 终结点映射器终结点 （端口 135）

   Azure AD 密码保护代理安装程序会自动创建一个允许访问端口 135 的 Windows 防火墙入站的规则。 如果此规则更高版本已删除或禁用，DC 代理将不能与代理服务进行通信。 如果内置 Windows 防火墙已被禁用而不是另一个防火墙产品，则必须配置防火墙以允许访问端口 135。

1. 代理主机正在阻止访问 RPC 终结点 （动态或静态） 上侦听代理服务

   Azure AD 密码保护代理安装程序会自动创建 Windows 防火墙入站的规则，允许任何入站端口访问所侦听的 Azure AD 密码保护代理服务。 如果此规则更高版本已删除或禁用，DC 代理将不能与代理服务进行通信。 如果内置 Windows 防火墙已被禁用而不是另一个防火墙产品，则必须配置防火墙以允许任何入站端口访问所侦听的 Azure AD 密码保护代理服务。 此配置可能会在更具体，如果代理服务已配置为侦听特定的静态 RPC 端口 (使用`Set-AzureADPasswordProtectionProxyConfiguration`cmdlet)。

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>代理服务可以接收来自域中的 DC 代理调用，但无法与 Azure 通信

1. 确保代理计算机已连接到的终结点中列出[部署要求](howto-password-ban-bad-on-premises-deploy.md)。

1. 确保林和针对相同的 Azure 租户中注册服务器的所有代理。

   可以通过运行检查这`Get-AzureADPasswordProtectionProxy`并`Get-AzureADPasswordProtectionDCAgent`PowerShell cmdlet，然后比较`AzureTenant`每个属性返回项。 对于正确操作这些必须是相同的林中跨所有 DC 代理和代理服务器。

   如果不存在的 Azure 租户注册不匹配条件，这可以修复通过运行`Register-AzureADPasswordProtectionProxy`和/或`Register-AzureADPasswordProtectionForest`PowerShell cmdlet，根据需要并确保为所有注册使用同一个 Azure 租户中的凭据。

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>DC 代理都无法进行加密或解密密码策略文件和其他状态

此问题可能表现了大量的症状，但通常具有常见的根本原因。

Azure AD 密码保护有一个关键依赖项上提供的 Microsoft 密钥分发服务，这是适用于运行 Windows Server 2012 的域控制器及更高版本的加密和解密功能。 KDS 服务必须启用并在所有 Windows Server 2012 和更高版本的域中的域控制器上正常运行。

默认情况下 KDS 服务的服务启动模式被配置为手动 （触发器启动）。 这种配置意味着，第一次客户端尝试使用该服务，它是按需启动。 若要运行的 Azure AD 密码保护的可接受此默认服务启动模式。

如果已为已禁用配置 KDS 服务启动模式，Azure AD 密码保护将正常工作之前，必须修复此配置。

此问题的简单测试是手动启动 KDS 服务，无论是通过服务管理 MMC 控制台，或使用其他服务管理工具 （例如，运行"net start kdssvc"从命令提示符下控制台）。 KDS 服务应能够成功启动，并保持运行。

当前无法启动的 KDS 服务的最常见根本原因是外部默认域控制器 OU 的 Active Directory 域控制器对象。 此配置不支持由 KDS 服务，而不是 Azure AD 密码保护规定的限制。 这种情况的解决方法是将域控制器对象移动到下的默认域控制器 OU 的某个位置。

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>弱密码已被接受，但不是应为

此问题可能有几个原因。

1. DC 代理无法下载策略，或无法解密现有策略。 检查有关上述主题中可能的原因。

1. 密码策略强制模式仍设置为“审核”。 如果此配置生效，重新为强制使用 Azure AD 密码保护门户中配置它。 请参阅[启用密码保护](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

1. 密码策略已被禁用。 如果此配置生效，重新配置它以使用 Azure AD 密码保护门户启用。 请参阅[启用密码保护](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

1. 你未在域中的所有域控制器上安装 DC 代理软件。 在此情况下，很难确保远程 Windows 客户端密码更改操作的过程目标的特定域控制器。 如果您具有认为已成功针对特定 DC 安装 DC 代理软件，你可以通过复查 DC 代理管理事件日志中验证： 而不考虑结果，将有至少一个事件记录的密码的结果验证。 如果没有为用户更改其密码时提供的事件，则密码更改已可能处理由不同的域控制器。

   作为备用的测试，请尝试 setting\changing 密码而直接登录到安装 DC 代理软件的 DC。 此方法不建议用于生产 Active Directory 域。

   尽管增量部署的 DC 代理软件支持这些限制，Microsoft 强烈建议 DC 代理软件上安装了域中的所有域控制器越早越好。

1. 实际上可能会在按预期方式使用的密码验证算法。 请参阅[如何评估密码](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="directory-services-repair-mode"></a>目录服务修复模式

如果域控制器启动进入目录服务修复模式中，则 DC 代理服务将检测这种情况，并将导致所有密码验证或强制活动处于禁用状态，而不考虑当前处于活动状态的策略配置。

## <a name="emergency-remediation"></a>紧急补救

如果 DC 代理服务造成了问题，可以立即关闭 DC 代理服务。 DC 代理密码筛选器 dll 仍会尝试调用未运行的服务并记录警告事件（10012、10013），但在此期间会接受所有传入的密码。 然后，也可以通过 Windows 服务控制管理器并根据需要使用启动类型“Disabled”来配置 DC 代理服务。

另一种补救措施是在 Azure AD 密码保护门户中将“启用”模式设置为“否”。 下载更新的策略后，各 DC 代理服务将进入静默模式，在这种模式下，将按原样接受所有密码。 有关详细信息，请参阅[强制模式](howto-password-ban-bad-on-premises-operations.md#enforce-mode)。

## <a name="domain-controller-demotion"></a>域控制器降级

支持降级仍在运行 DC 代理软件的域控制器。 但管理员应注意，在降级过程中，DC 代理软件会继续实施当前密码策略。 将像验证其他任何密码一样验证（降级操作过程中指定的）新本地管理员帐户密码。 Microsoft 建议在 DC 降级过程中为本地管理员帐户选择安全密码；但是，现有的降级操作过程可能会中断 DC 代理软件对新本地管理员帐户密码执行的验证。

成功降级后，如果域控制器已重新启动并再次以普通成员服务器的形式运行，则 DC 代理软件将恢复为以被动模式运行。 然后，随时可将其卸载。

## <a name="removal"></a>删除

如果决定卸载 Azure AD 密码保护软件和清理的域和林从所有相关的状态，可以使用以下步骤完成此任务：

> [!IMPORTANT]
> 必须按顺序执行这些步骤。 如果代理服务的任何实例仍在运行，它会定期重新创建其 serviceConnectionPoint 对象。 如果 DC 代理服务的任何实例仍在运行，它会定期重新创建其 serviceConnectionPoint 对象和 sysvol 状态。

1. 从所有计算机中卸载代理软件。 完成此步骤后**不需要**重新启动。
2. 从所有域控制器中卸载 DC 代理软件。 完成此步骤后**需要**重新启动。
3. 在每个域命名上下文中手动删除所有代理服务连接点。 可使用以下 Active Directory PowerShell 命令发现这些对象的位置：

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   不要省略 $keywords 变量值末尾的星号（“*”）。

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。

4. 在每个域命名上下文中手动删除所有 DC 代理连接点。 可能有每个域控制器的林，具体取决于部署软件的目标范围中这些对象。 可使用以下 Active Directory PowerShell 命令发现该对象的位置：

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。

   不要省略 $keywords 变量值末尾的星号（“*”）。

5. 手动删除林级配置状态。 林配置状态保留在 Active Directory 配置命名上下文中的某个容器内。 可按如下所示发现和删除该状态：

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. 通过手动删除以下文件夹及其所有内容，手动删除 sysvol 相关的所有状态：

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   如果需要，也可以在给定域控制器的本地访问此路径；默认位置类似于以下路径：

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   如果在非默认位置配置了 sysvol 共享，则此路径不同。

## <a name="next-steps"></a>后续步骤

[Azure AD 密码保护的常见问题解答](howto-password-ban-bad-on-premises-faq.md)

有关全局和自定义禁止密码列表的详细信息，请参阅[禁止错误的密码](concept-password-ban-bad.md)一文
