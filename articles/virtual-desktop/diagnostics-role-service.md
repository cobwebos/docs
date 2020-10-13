---
title: Windows 虚拟桌面诊断问题 - Azure
description: 如何使用 Windows 虚拟桌面诊断功能来诊断问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70676bd1a07acdfcbba071a906b390ed66d70074
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91279852"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>确定和诊断 Windows 虚拟桌面问题

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)。

Windows 虚拟桌面提供了一项诊断功能，使管理员能够通过单个界面识别问题。 若要了解有关 Windows 虚拟桌面诊断功能的详细信息，请参阅 [将 Log Analytics 用于诊断功能](diagnostics-log-analytics.md)。

由于诊断角色服务本身是 Windows 虚拟桌面的一部分，因此无法访问 Windows 虚拟桌面的连接将不会显示在诊断结果中。 当最终用户遇到网络连接问题时，可能会出现 Windows 虚拟桌面连接问题。

## <a name="common-error-scenarios"></a>常见错误方案

WVDErrors 表跟踪所有活动类型的错误。 名为 "ServiceError" 的列提供了标记为 "True" 或 "False" 的其他标志。 此标志将告知错误是否与服务相关。

* 如果该值为 "True"，则服务团队可能已调查此问题。 如果这会影响用户体验并显示较长的时间，我们建议为 Windows 虚拟桌面提交支持票证。
* 如果值为 "False"，则这可能是一种配置错误，您可以自行解决。 错误消息可为您显示有关从何处开始的线索。

下表列出了管理员可能会遇到的常见错误。

>[!NOTE]
>此列表包括最常见的错误，并且会定期更新。 若要确保获得最新的信息，请务必每月至少查看一次本文。

## <a name="management-errors"></a>管理错误

|错误消息|建议的解决方案|
|---|---|
|未能创建注册密钥 |无法创建注册令牌。 请尝试使用较短的到期时间 (1 小时到1个月) 。 |
|未能删除注册密钥|无法删除注册令牌。 请尝试再次删除它。 如果仍不起作用，请使用 PowerShell 检查令牌是否仍然存在。 如果存在，请将它与 PowerShell 一起删除。|
|未能更改会话主机排出模式 |无法更改 VM 的排出模式。 检查 VM 状态。 如果 VM 不可用，则无法更改排出模式。|
|未能断开用户会话连接 |无法断开用户与 VM 的连接。 检查 VM 状态。 如果 VM 不可用，则无法断开用户会话的连接。 如果 VM 可用，请检查用户会话状态以查看它是否已断开连接。 |
|未能注销会话主机内的所有用户 ()  |无法将用户从 VM 注销。 检查 VM 状态。 如果不可用，则无法注销用户。检查用户会话状态，以查看它们是否已注销。可以通过 PowerShell 强制退出。 |
|未能从应用程序组中取消分配用户|无法取消发布用户的应用组。 查看 Azure AD 上的用户是否可用。 查看用户是否属于应用组发布到的用户组。 |
|检索可用位置时出错 |检查在创建主机池向导中使用的 VM 的位置。 如果映像在该位置不可用，请在该位置添加映像，或者选择其他 VM 位置。 |

### <a name="connection-error-codes"></a>连接错误代码

|数字代码|错误代码|建议的解决方案|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|会话主机未正确加入到 Active Directory。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|由于会话主机不可用，连接失败。 请检查会话主机的运行状况。|
|-2146233088|ConnectionFailedClientDisconnect|如果经常看到此错误，请确保用户的计算机已连接到网络。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|主机用户尝试连接到的会话不正常。 调试虚拟机。|
|-2146233088|ConnectionFailedUserNotAuthorized|用户无权访问已发布的应用或桌面。 在管理员删除已发布资源后，可能会出现此错误。 要求用户刷新远程桌面应用程序中的源。|
|2|FileNotFound|用户尝试访问的应用程序未正确安装或设置为不正确的路径。<br><br>当在用户具有活动会话的情况下发布新应用时，用户将无法访问此应用。 必须关闭并重新启动会话，然后用户才能访问应用。 |
|3|InvalidCredentials|用户输入的用户名或密码与任何现有的用户名或密码都不匹配。 请查看凭据是否有拼写错误，然后重试。|
|8|ConnectionBroken|客户端和网关或服务器之间的连接已断开。 除非意外发生，否则不需要执行任何操作。|
|14|UnexpectedNetworkDisconnect|断开与网络的连接。 要求用户再次连接。|
|24|ReverseConnectFailed|主机虚拟机没有直通 RD 网关。 请确保可以解析网关 IP 地址。|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>错误：无法向应用组添加用户分配

将用户分配到应用组后，Azure 门户会显示一条警告，指出 "会话结束" 或 "遇到身份验证问题-扩展 Microsoft_Azure_WVD。" 然后，"分配" 页将不会加载，此后，页面将停止在整个 Azure 门户 (加载，例如 Azure Monitor、Log Analytics、服务运行状况等) 。

**原因：** 条件性访问策略有问题。 Azure 门户正在尝试获取 Microsoft Graph 的令牌，该令牌依赖于 SharePoint Online。 客户具有名为 "Microsoft Office 365 数据存储使用条款" 的条件性访问策略，该策略要求用户接受访问数据存储所需的使用条款。 但是，它们尚未登录，因此 Azure 门户无法获取令牌。

**修复：** 在登录到 Azure 门户之前，管理员首先需要登录到 SharePoint 并接受使用条款。 之后，他们应该能够像平常一样登录到 Azure 门户。

## <a name="next-steps"></a>后续步骤

若要了解有关 Windows 虚拟桌面中的角色的详细信息，请参阅 [Windows 虚拟桌面环境](environment-setup.md)。

若要查看适用于 Windows 虚拟桌面的可用 PowerShell cmdlet 的列表，请参阅 [PowerShell 参考](/powershell/windows-virtual-desktop/overview)。
