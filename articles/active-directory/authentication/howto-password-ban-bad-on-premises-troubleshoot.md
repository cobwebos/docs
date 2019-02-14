---
title: Azure AD 密码保护预览版中的故障排除
description: 了解 Azure AD 密码保护预览版的常见故障排除方法
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
ms.openlocfilehash: 5727965373752d40e3ce508c1bc79046c2b3b70b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177745"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>预览版：Azure AD 密码保护故障排除

|     |
| --- |
| Azure AD 密码保护是 Azure Active Directory 的一项公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

部署 Azure AD 密码保护后，可能需要进行故障排除。 本文详细介绍如何帮助你了解一些常见的故障排除步骤。

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>不按预期拒绝弱密码

这可能由多种原因造成：

1. DC 代理尚未下载策略。 其症状为 DC 代理管理员事件日志中的 30001 事件。

    可能导致此问题的原因包括：

    1. 尚未注册林
    2. 尚未注册代理
    3. 网络连接问题阻止代理服务与 Azure 通信（检查 HTTP 代理要求）

2. 密码策略强制模式仍设置为“审核”。 如果是这种情况，请使用 Azure AD 密码保护门户将其重新配置为“强制”。 请参阅[启用密码保护](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

3. 密码策略已被禁用。 如果是这种情况，请使用 Azure AD 密码保护门户将其重新配置为“已启用”。 请参阅[启用密码保护](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

4. 密码验证算法可能按预期方式工作。 请参阅[如何评估密码](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="directory-services-repair-mode"></a>目录服务修复模式

如果域控制器启动进入目录服务修复模式，则无论当前处于活动状态的策略配置是什么，DC 代理服务都会检测此模式，并导致所有密码验证或实施活动被禁用。

## <a name="emergency-remediation"></a>紧急补救

如果 DC 代理服务造成了问题，可以立即关闭 DC 代理服务。 DC 代理密码筛选器 dll 仍会尝试调用未运行的服务并记录警告事件（10012、10013），但在此期间会接受所有传入的密码。 然后，也可以通过 Windows 服务控制管理器并根据需要使用启动类型“Disabled”来配置 DC 代理服务。

另一种补救措施是在 Azure AD 密码保护门户中将“启用”模式设置为“否”。 下载更新的策略后，各 DC 代理服务将进入静默模式，在这种模式下，将按原样接受所有密码。 有关详细信息，请参阅[强制模式](howto-password-ban-bad-on-premises-operations.md#enforce-mode)。

## <a name="domain-controller-demotion"></a>域控制器降级

支持降级仍在运行 DC 代理软件的域控制器。 但管理员应注意，在降级过程中，DC 代理软件会继续实施当前密码策略。 将像验证其他任何密码一样验证（降级操作过程中指定的）新本地管理员帐户密码。 Microsoft 建议在 DC 降级过程中为本地管理员帐户选择安全密码；但是，现有的降级操作过程可能会中断 DC 代理软件对新本地管理员帐户密码执行的验证。

成功降级后，如果域控制器已重新启动并再次以普通成员服务器的形式运行，则 DC 代理软件将恢复为以被动模式运行。 然后，随时可将其卸载。

## <a name="removal"></a>删除

如果决定卸载公共预览版软件并从域和林中清理所有相关状态，可使用以下步骤完成此任务：

> [!IMPORTANT]
> 必须按顺序执行这些步骤。 如果代理服务的任何实例仍在运行，它会定期重新创建其 serviceConnectionPoint 对象。 如果 DC 代理服务的任何实例仍在运行，它会定期重新创建其 serviceConnectionPoint 对象和 sysvol 状态。

1. 从所有计算机中卸载代理软件。 完成此步骤后**不需要**重新启动。
2. 从所有域控制器中卸载 DC 代理软件。 完成此步骤后**需要**重新启动。
3. 在每个域命名上下文中手动删除所有代理服务连接点。 可使用以下 Active Directory PowerShell 命令发现这些对象的位置：

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   不要省略 $keywords 变量值末尾的星号（“*”）。

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。 

4. 在每个域命名上下文中手动删除所有 DC 代理连接点。 根据公共预览版软件的部署范围，林中的每个域控制器可能存在其中的一个对象。 可使用以下 Active Directory PowerShell 命令发现该对象的位置：

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。

   不要省略 $keywords 变量值末尾的星号（“*”）。

5. 手动删除林级配置状态。 林配置状态保留在 Active Directory 配置命名上下文中的某个容器内。 可按如下所示发现和删除该状态：

   ```PowerShell
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
