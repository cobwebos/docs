---
title: Windows 虚拟桌面诊断问题-Azure
description: 如何使用 Windows 虚拟桌面诊断功能来诊断问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cffc6393ef6f5c1a33be615d9d5d4b8729ab711f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611851"
---
# <a name="identify-and-diagnose-issues"></a>识别和诊断问题

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虚拟桌面提供一项诊断功能，使管理员能够通过单个界面识别问题。 若要了解有关 Windows 虚拟桌面诊断功能的详细信息，请参阅[将 Log Analytics 用于诊断功能](diagnostics-log-analytics.md)。
  
由于诊断角色服务本身是 Windows 虚拟桌面的一部分，因此无法访问 Windows 虚拟桌面的连接将不会显示在诊断结果中。 当最终用户遇到网络连接问题时，可能会出现 Windows 虚拟桌面连接问题。

## <a name="common-error-scenarios"></a>常见错误方案

错误方案在服务的内部和 Windows 虚拟桌面的外部分类。

* 内部问题：指定客户无法缓解的方案，需要将这些方案解析为支持问题。 当通过[Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)提供反馈时，请包含问题发生时的相关 ID 和大致时间范围。
* 外部问题：与可由客户缓解的方案相关。 这些是 Windows 虚拟桌面的外部。

下表列出了管理员可能会遇到的常见错误。

>[!NOTE]
>此列表包括最常见的错误，并会定期更新。 若要确保获得最新的信息，请务必每月至少查看一次本文。

## <a name="management-errors"></a>管理错误

|错误消息|建议的解决方案|
|---|---|
|未能创建注册密钥 |无法创建注册令牌。 尝试使用较短的到期时间（介于1小时到1个月之间）重新创建。 |
|未能删除注册密钥|无法删除注册令牌。 请尝试再次删除它。 如果仍不起作用，请使用 PowerShell 检查令牌是否仍然存在。 如果存在，请将它与 PowerShell 一起删除。|
|未能更改会话主机排出模式 |无法更改 VM 的排出模式。 检查 VM 状态。 如果 VM 不可用，则无法更改排出模式。|
|未能断开用户会话连接 |无法断开用户与 VM 的连接。 检查 VM 状态。 如果 VM 不可用，则无法断开用户会话的连接。 如果 VM 可用，请检查用户会话状态以查看它是否已断开连接。 |
|未能注销会话主机内的所有用户 |无法将用户从 VM 注销。 检查 VM 状态。 如果不可用，则无法注销用户。检查用户会话状态，以查看它们是否已注销。可以通过 PowerShell 强制退出。 |
|未能从应用程序组中取消分配用户|无法取消发布用户的应用组。 查看 Azure AD 上的用户是否可用。 查看用户是否属于应用组发布到的用户组。 |
|检索可用位置时出错 |检查在创建主机池向导中使用的 VM 的位置。 如果映像在该位置不可用，请在该位置添加映像，或者选择其他 VM 位置。 |

### <a name="external-connection-error-codes"></a>外部连接错误代码

|数值代码|错误代码|建议的解决方案|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|会话主机未正确加入到 Active Directory。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|由于会话主机不可用，连接失败。 检查会话主机的运行状况。|
|-2146233088|ConnectionFailedClientDisconnect|如果经常看到此错误，请确保用户的计算机已连接到网络。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|主机用户尝试连接到的会话不正常。 调试虚拟机。|
|-2146233088|ConnectionFailedUserNotAuthorized|用户无权访问已发布的应用或桌面。 在管理员删除已发布资源后，可能会出现此错误。 要求用户刷新远程桌面应用程序中的源。|
|2|FileNotFound|用户尝试访问的应用程序未正确安装或设置为不正确的路径。<br><br>当在用户具有活动会话的情况下发布新应用时，用户将无法访问此应用。 必须关闭并重新启动会话，然后用户才能访问应用。 |
|3|InvalidCredentials|用户输入的用户名或密码与任何现有的用户名或密码都不匹配。 查看输入错误的凭据，然后重试。|
|8|ConnectionBroken|客户端和网关或服务器之间的连接已断开。 除非意外发生，否则不需要执行任何操作。|
|14|UnexpectedNetworkDisconnect|断开与网络的连接。 要求用户再次连接。|
|24|ReverseConnectFailed|主机虚拟机没有可直接连接到 RD 网关的行为。 确保可以解析网关 IP 地址。|

## <a name="next-steps"></a>后续步骤

若要了解有关 Windows 虚拟桌面中的角色的详细信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。

若要查看适用于 Windows 虚拟桌面的可用 PowerShell cmdlet 的列表，请参阅[PowerShell 参考](/powershell/windows-virtual-desktop/overview)。
