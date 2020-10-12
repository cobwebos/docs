---
title: 排查自助式密码重置写回问题 - Azure Active Directory
description: 了解如何排查在 Azure Active Directory 中进行自助式密码重置写回时遇到的常见问题及其解决方法步骤
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50e202d26574c0fc8adfeb7f73eb150ebb1781af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89663728"
---
# <a name="troubleshoot-self-service-password-reset-writeback-in-azure-active-directory"></a>排查 Azure Active Directory 中的自助式密码重置写回问题

使用 Azure Active Directory (Azure AD) 自助式密码重置 (SSPR)，用户可在云中重置自己的密码。 密码写回是使用 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 启用的功能，可将云中的密码更改实时写回到现有的本地目录。

如果你在 SSPR 写回方面遇到问题，可以参阅以下故障排除步骤和常见错误。 如果你找不到问题的解答，[我们的支持团队始终愿意提供](#contact-microsoft-support)进一步的帮助。

## <a name="troubleshoot-connectivity"></a>排查连接问题

如果 Azure AD Connect 的密码写回出现问题，请查看以下有助于解决此问题的步骤。 若要恢复你的服务，建议按顺序执行这些步骤：

* [确认网络连接](#confirm-network-connectivity)
* [重启 Azure AD Connect Sync 服务](#restart-the-azure-ad-connect-sync-service)
* [禁用再重新启用密码写回功能](#disable-and-re-enable-the-password-writeback-feature)
* [安装最新版本的 Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [排查密码写回问题](#common-password-writeback-errors)

### <a name="confirm-network-connectivity"></a>确认网络连接

最常见的故障点是防火墙或代理端口或空闲超时未正确配置。

对于 Azure AD Connect 1.1.443.0 及更高版本，需要对以下地址的出站 HTTPS 访问权限：

* *\*。 passwordreset.microsoftonline.com*
* *\*。 servicebus.windows.net*

如果需要更多详细信息，请参阅 [Microsoft Azure 数据中心 IP 范围的列表](https://www.microsoft.com/download/details.aspx?id=41653)。 此列表每周三更新一次，并在下个星期一生效。

有关详细信息，请参阅 [Azure AD Connect 的连接先决条件](../hybrid/how-to-connect-install-prerequisites.md)。

### <a name="restart-the-azure-ad-connect-sync-service"></a>重启 Azure AD Connect Sync 服务

若要解决该服务的连接性问题或其他暂时性问题，请完成以下步骤来重启 Azure AD Connect Sync 服务：

1. 在运行 Azure AD Connect 的服务器上以管理员身份选择“启动”。
1. 在搜索字段中输入 *services.msc*，按 **Enter**。
1. 找到“Microsoft Azure AD Sync”条目。
1. 右键单击该服务条目，选择“重启”，然后等待操作完成。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/service-restart.png" alt-text="使用 GUI 重启 Azure AD Sync 服务" border="false":::

这些步骤将重新建立与 Azure AD 的连接，应该能够解决你的连接性问题。

如果重启 Azure AD Connect Sync 服务无法解决问题，请尝试在下一部分禁用密码写回功能，然后再将其重新启用。

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>禁用再重新启用密码写回功能

若要继续排查问题，请完成以下步骤来禁用密码写回功能，然后再将其重写启用：

1. 在运行 Azure AD Connect 的服务器上以管理员身份打开“Azure AD Connect 配置向导”。
1. 在“连接到 Azure AD”中，输入 Azure AD 全局管理员凭据。
1. 在“连接到 AD DS”中，输入你的本地 Active Directory 域服务管理员凭据。
1. 在“唯一标识用户”中，选择“下一步”按钮。 
1. 在“可选功能”中，清除“密码写回”复选框。 
1. 选择“下一步”完成剩余的对话框页，而不更改任何内容，直到转到“准备好配置”页。 
1. 检查“准备好配置”页面是否将“密码写回”选项显示为“已禁用”。 选择绿色的“配置”按钮以提交更改。
1. 在“已完成”中，清除“立即同步”选项，并选择“完成”关闭向导。  
1. 重新打开“Azure AD Connect 配置向导”。
1. 重复步骤 2-8，但这次请选中“可选功能”页上的“密码写回”选项以重新启用该服务。

这些步骤将重新建立与 Azure AD 的连接，应该能够解决你的连接性问题。

如果禁用密码写回功能后再将其重写启用无法解决问题，请在下一部分重新安装 Azure AD Connect。

### <a name="install-the-latest-azure-ad-connect-release"></a>安装最新版本的 Azure AD Connect

重新安装 Azure AD Connect 可以解决 Azure AD 与本地 Active Directory 域服务环境之间的配置和连接性问题。 建议先尝试前面的步骤来验证和排查连接性问题，最后再执行此步骤。

> [!WARNING]
> 如果你自定义了现成的同步规则，请先备份这些规则，再继续进行升级，并在完成后手动重新部署这些规则。

1. 从 [Microsoft 下载中心](https://go.microsoft.com/fwlink/?LinkId=615771)下载最新版本的 Azure AD Connect。
1. 由于你已安装 Azure AD Connect，请执行就地升级将 Azure AD Connect 安装更新到最新版本。

    运行下载的程序包，然后按屏幕说明更新 Azure AD Connect。

这些步骤应当会重新建立与 Azure AD 的连接，应该能够解决你的连接性问题。

如果安装最新版本的 Azure AD Connect 服务器无法解决问题，请在安装最新版本之后尝试禁用密码写回，然后再将其重新启用，这是最后的手段。

## <a name="verify-that-azure-ad-connect-has-the-required-permissions"></a>验证 Azure AD Connect 是否具有所需的权限

Azure AD Connect 需要 AD DS“重置密码”权限才能执行密码写回。 若要检查 Azure AD Connect 是否具有给定的本地 AD DS 用户帐户的所需权限，可使用 **Windows 有效权限**功能：

1. 登录到 Azure AD Connect 服务器，并选择“开始” > “同步服务”启动“Synchronization Service Manager”。
1. 在“连接器”选项卡下，选择本地“Active Directory 域服务器”连接器，并选择“属性”。  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager.png" alt-text="使用 GUI 重启 Azure AD Sync 服务" border="false":::
  
1. 在弹出窗口中，选择“连接到 Active Directory 林”，并记下“用户名”属性 。 此属性是 Azure AD Connect 用于执行目录同步的 AD DS 帐户。

    若要让 Azure AD Connect 执行密码写回，AD DS 帐户必须具有重置密码权限。 通过以下步骤检查对此用户帐户的权限。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager-properties.png" alt-text="使用 GUI 重启 Azure AD Sync 服务" border="false":::
  
1. 登录到本地域控制器并启动“Active Directory 用户和计算机”应用程序。
1. 选择“视图”并确保已启用“高级功能”选项 。  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-advanced-features.png" alt-text="使用 GUI 重启 Azure AD Sync 服务" border="false":::
  
1. 查找要验证的 AD DS 用户帐户。 右键单击帐户名称并选择“属性”。  
1. 在弹出窗口中，转到“安全”选项卡，并选择“高级” 。  
1. 在“管理员的高级安全设置”弹出窗口中，转到“有效访问权限”选项卡 。
1. 依次选择“选择用户”、Azure AD Connect 使用的 AD DS 帐户、“查看有效访问权限” 。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-effective-access.png" alt-text="使用 GUI 重启 Azure AD Sync 服务" border="false":::
  
1. 向下滚动并查找“重置密码”。 如果已选中该条目，则表示 AD DS 帐户有权重置所选 Active Directory 用户帐户的密码。  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/check-permissions.png" alt-text="使用 GUI 重启 Azure AD Sync 服务" border="false":::

## <a name="common-password-writeback-errors"></a>常见的密码写回错误

密码写回可能会出现以下更具体的问题。 如果遇到上述错误之一，请查看建议的解决方案并检查密码写回是否正常工作。

| 错误 | 解决方案 |
| --- | --- |
| 密码重置服务没有在本地启动。 Azure AD Connect 计算机的应用程序事件日志中出现错误 6800。 <br> <br> 加入后，联合身份验证、直通身份验证或密码哈希同步的用户无法重置其密码。 | 当启用了密码写回时，同步引擎将调用写回库通过与云登记服务进行通信来执行配置（登记）。 在登记期间或者为密码写回启动 Windows Communication Foundation (WCF) 终结点时遇到任何错误都将导致在 Azure AD Connect 计算机的事件日志中生成错误。 <br> <br> 在重启 Azure AD Sync (ADSync) 服务期间，如果配置了写回，则 WCF 终结点将启动。 但是，如果终结点启动失败，我们将记录事件 6800 并允许同步服务启动。 存在此事件意味着密码写回终结点未启动。 此事件 6800 的事件日志详细信息以及 PasswordResetService 组件生成的事件日志条目将指明终结点无法启动的原因。 请查看这些事件日志错误，如果密码写回仍不能正常工作，请尝试重启 Azure AD Connect。 如果问题仍然存在，请尝试禁用并重新启用密码写回。
| 如果用户尝试重置密码或解锁启用了密码写回功能的帐户，则操作会失败。 <br> <br> 此外，在解锁操作执行后，Azure AD Connect 事件日志中将显示一个事件，其中包含：“同步引擎返回了错误 hr=800700CE，消息=文件名或扩展名太长”。 | 查找用于 Azure AD Connect 的 Active Directory 帐户并重置密码，使其包含的字符数不超过 256 个。 接下来，从“开始”菜单打开“同步服务”。  浏览到“连接器”并找到“Active Directory 连接器”。  选择它，然后选择“属性”。 浏览到“凭据”页，并输入新密码。 选择“确定”关闭页面。 |
| 在 Azure AD Connect 安装过程的最后一步，看到了一个错误，它指出无法配置密码写回。 <br> <br> Azure AD Connect 应用程序事件日志包含错误 32009，其文本为“获取身份验证令牌时出错”。 | 在以下两种情况下会发生此错误： <br><ul><li>为在 Azure AD Connect 安装过程开始时提供的全局管理员帐户指定了错误的密码。</li><li>试图将联合用户用于在 Azure AD Connect 安装过程开始时指定的全局管理员帐户。</li></ul> 若要解决此问题，请确保未将联合帐户用于在安装过程开始时指定的全局管理员，并且指定的密码正确无误。 |
| Azure AD Connect 计算机事件日志包含运行 PasswordResetService 时引发的错误 32002。 <br> <br> 错误内容如下：“连接到 ServiceBus 时出错。 令牌提供程序无法提供安全令牌。” | 本地环境无法连接到云中的 Azure 服务总线终结点。 此错误是由于防火墙规则阻止了到特定端口或 web 地址的出站连接导致的。 有关详细信息，请参阅[连接先决条件](../hybrid/how-to-connect-install-prerequisites.md)。 在更新这些规则后，重启 Azure AD Connect 服务器，密码写回应当会再次开始工作。 |
| 工作一段时间后，联合身份验证、直通身份验证或密码哈希同步的用户无法重置其密码。 | 在某些极少见的情况下，当 Azure AD Connect 已重启时，密码写回服务可能无法重启。 在这些情况下，请首先检查是否已在本地启用了密码写回。 可以使用 Azure AD Connect 向导或 PowerShell 执行检查。 如果此功能显示为已启用，请尝试再次启用或禁用此功能。 如果此故障排除步骤不起作用，请尝试完全卸载并重新安装 Azure AD Connect。 |
| 尝试重置其密码的联合身份验证、直通身份验证或密码哈希同步的用户在尝试提交密码后看到错误。 该错误指示存在服务问题。 <br ><br> 除此问题以外，在密码重置期间，可能会在本地事件日志中看到有关管理代理被拒绝访问的消息。 | 如果在事件日志中看到这些错误，请确认 Active Directory 管理代理 (ADMA) 帐户（在配置时在向导中指定的帐户）具有进行密码写回所需的权限。 <br> <br> 在授予此权限后，权限可能需要最多一小时来通过域控制器 (DC) 上的 `sdprop` 后台任务进行渗透。 <br> <br> 要使密码重置工作，需要将此权限标记在为其重置密码的用户对象的安全描述符上。 在此权限显示在用户对象上之前，密码重置将持续失败并出现“访问被拒绝”消息。 |
| 尝试重置其密码的联合身份验证、直通身份验证或密码哈希同步的用户在提交密码后看到错误。 该错误指示存在服务问题。 <br> <br> 除此问题以外，在密码重置期间，可能会在 Azure AD Connect 服务的事件日志中看到一个表示“找不到对象”错误的错误。 | 此错误通常表示同步引擎无法找到 Azure AD 连接器空间中的用户对象或者无法找到链接的 Metaverse (MV) 或 Azure AD 连接器空间对象。 <br> <br> 若要解决此问题，请确保用户确实已通过当前的 Azure AD Connect 实例从本地同步到了 Azure AD，并检查连接器空间和 MV 中的对象的状态。 通过“Microsoft.InfromADUserAccountEnabled.xxx”规则确认 Active Directory 证书服务 (AD CS) 对象是否已连接到 MV 对象。|
| 尝试重置其密码的联合身份验证、直通身份验证或密码哈希同步的用户在提交密码后看到错误。 该错误指示存在服务问题。 <br> <br> 除此问题以外，在密码重置操作过程中，可能会在 Azure AD Connect 服务的事件日志中看到一个错误，指出发生“找到多个匹配项”错误。 | 这表示同步引擎通过“Microsoft.InfromADUserAccountEnabled.xxx”检测到 MV 对象连接到了多个 AD CS 对象。 这意味着用户在多个林具有已启用的帐户。 密码写回不支持此方案。 |
| 密码操作因为发生配置错误而失败。 应用程序事件日志包含 Azure AD Connect 错误 6329，文本为：“0x8023061f (操作失败，因为未在此管理代理上启用密码同步)”。 | 如果在启用密码写回功能之后将 Azure AD Connect 配置更改为添加新的 Active Directory 林（或者更改为删除现有林之后再重新添加），则会发生此错误。 在这些最近添加的林中，用户的密码操作会失败。 要解决此问题，请在林配置更改完成后，先禁用密码写回功能，再重新启用它。 |

## <a name="password-writeback-event-log-error-codes"></a>密码写回事件日志错误代码

在排查密码写回问题时，最佳做法是检查 Azure AD Connect 计算机上的应用程序事件日志。 此事件日志包含来自密码写回的两个源的事件。 PasswordResetService 源描述与密码写回操作相关的操作和问题。 ADSync 源描述与在 Active Directory 域服务环境中设置密码相关的操作和问题。

### <a name="if-the-source-of-the-event-is-adsync"></a>如果事件的源是 ADSync

| 代码 | 名称或消息 | 说明 |
| --- | --- | --- |
| 6329 | BAIL:MMS(4924) 0x80230619:“某个限制阻止将密码更改为当前指定的密码”。 | 当密码写回服务尝试在本地目录中设置的密码不符合域在密码期限、历史记录、复杂度或筛选方面的要求时，将发生此事件。 <br> <br> 如果使用最短密码期限，并且最近在此时间窗口内已更改过密码，将无法再次更改密码，直到它达到域中指定的期限。 对于测试目的，最短期限应设置为 0。 <br> <br> 如果启用了密码历史记录要求，则必须选择在最近 N 次未使用过的密码，其中 N 是密码历史记录设置。 如果选择了在最近 *N* 次中使用过的密码，则在此情况下会失败。 对于测试目的，密码历史记录应设置为 0。 <br> <br> 如果有密码复杂性要求，则当用户尝试更改或重置密码时会强制实施所有这些要求。 <br> <br> 如果启用密码筛选器，并且用户选择了不满足筛选条件的密码，则重置或更改操作会失败。 |
| 6329 | MMS(3040): admaexport.cpp(2837):服务器不包含 LDAP 密码策略控件。 | 如果未在 DC 中启用 LDAP_SERVER_POLICY_HINTS_OID 控件 (1.2.840.113556.1.4.2066)，将会发生此问题。 要使用密码写回功能，必须启用该控件。 为此，DC 必须位于 Windows Server 2008R2 或更高版本上。 |
| HR 8023042 | 同步引擎返回了错误：hr = 80230402，消息 = 由于存在使用相同定位点的重复条目，尝试获取对象失败。 | 在多个域中启用同一用户 ID 时会发生此错误。 例如，如果正在同步帐户和资源林，并且每个林中存在并启用了同一个用户 ID，则会发生此错误。 <br> <br> 如果使用了不唯一的定位点属性（如别名或 UPN），并且两个用户共享了这同一个定位点属性，则也可能发生此错误。 <br> <br> 若要解决此问题，请确保域中没有任何重复的用户，并且每个用户使用唯一的定位点属性。 |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>如果事件源是 PasswordResetService

| 代码 | 名称或消息 | 说明 |
| --- | --- | --- |
| 31001 | PasswordResetStart | 此事件表示本地服务检测到从云端发出联合身份验证、直通身份验证或密码哈希同步的用户的密码重置请求。 此事件是每个密码重置写回操作中的第一个事件。 |
| 31002 | PasswordResetSuccess | 此事件表示用户在密码重置操作过程中选择了一个新密码。 我们确定该密码满足企业密码要求。 该密码已成功写回到本地 Active Directory 环境。 |
| 31003 | PasswordResetFail | 此事件表示用户选择了一个密码，并且该密码已成功到达本地环境。 但是，当我们尝试在本地 Active Directory 环境中设置该密码时发生失败。 失败的原因可能包括： <br><ul><li>用户的密码不满足域在期限、历史记录、复杂性或筛选器方面的要求。 若要解决此问题，请创建新密码。</li><li>ADMA 服务帐户没有合适的权限在相关的用户帐户上设置新密码。</li><li>该用户的帐户位于不允许密码设置操作的受保护组（例如，域管理员或企业管理员组）中。</li></ul>|
| 31004 | OnboardingEventStart | 如果为 Azure AD Sync 启用了密码写回，并且我们已开始将组织登记到密码写回 Web 服务，则会发生此事件。 |
| 31005 | OnboardingEventSuccess | 此事件表示加入过程成功，并且密码写回功能已就绪可用。 |
| 31006 | ChangePasswordStart | 此事件表示本地服务检测到从云端发出联合身份验证、直通身份验证或密码哈希同步的用户的密码更改请求。 此事件是每个密码更改写回操作中的第一个事件。 |
| 31007 | ChangePasswordSuccess | 此事件表示用户在密码更改操作过程中选择了一个新密码，我们确定该密码满足公司密码要求，并且该密码已成功写回到本地 Active Directory 环境。 |
| 31008 | ChangePasswordFail | 此事件表示用户选择了一个密码，并且该密码已成功到达本地环境，但当我们尝试在本地 Active Directory 环境中设置该密码时出现错误。 失败的原因可能包括： <br><ul><li>用户的密码不满足域在期限、历史记录、复杂性或筛选器方面的要求。 若要解决此问题，请创建新密码。</li><li>ADMA 服务帐户没有合适的权限在相关的用户帐户上设置新密码。</li><li>该用户的帐户位于不允许密码设置操作的受保护组（例如，域管理员或企业管理员组）中。</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | 本地服务检测到管理员代表某用户发出联合身份验证、直通身份验证或密码哈希同步的用户的密码重置请求。 此事件是每个由管理员启动的密码重置写回操作中的第一个事件。 |
| 31010 | ResetUserPasswordByAdminSuccess | 管理员在管理员启动的密码重置操作过程中选择了一个新密码。 我们确定该密码满足企业密码要求。 该密码已成功写回到本地 Active Directory 环境。 |
| 31011 | ResetUserPasswordByAdminFail | 管理员代表用户选择了一个密码。 密码已成功到达本地环境。 但是，当我们尝试在本地 Active Directory 环境中设置该密码时发生失败。 失败的原因可能包括： <br><ul><li>用户的密码不满足域在期限、历史记录、复杂性或筛选器方面的要求。 尝试使用新密码来解决此问题。</li><li>ADMA 服务帐户没有合适的权限在相关的用户帐户上设置新密码。</li><li>该用户的帐户位于不允许密码设置操作的受保护组（例如，域管理员或企业管理员组）中。</li></ul>  |
| 31012 | OffboardingEventStart | 如果为 Azure AD Sync 禁用了密码写回，则会发生此事件，它表示我们已开始将组织卸载到密码写回 web 服务。 |
| 31013| OffboardingEventSuccess| 此事件表示脱离过程成功并且已成功地禁用了密码写回功能。 |
| 31014| OffboardingEventFail| 此事件表示卸载过程没有成功。 这可能是由于在配置期间指定的云管理员帐户或本地管理员帐户存在权限错误导致的。 也可能是由于在禁用密码写回时尝试使用了联合云全局管理员导致的。 要解决此问题，请检查管理权限并确保在配置密码写回功能时未使用任何联合帐户。|
| 31015| WriteBackServiceStarted| 此事件表示密码写回服务已成功启动。 该服务已准备好接受来自云的密码管理请求。|
| 31016| WriteBackServiceStopped| 此事件表示密码写回服务已停止。 来自云的任何密码管理请求都不会成功。|
| 31017| AuthTokenSuccess| 此事件表示为启动卸载或登记过程，我们已成功检索到在设置 Azure AD Connect 期间指定的全局管理员的授权令牌。|
| 31018| KeyPairCreationSuccess| 此事件表示已成功创建密码加密密钥。 此密钥用于对从云发送到本地环境的密码进行加密。|
| 32000| UnknownError| 此事件表示在执行密码管理操作期间发生未知的错误。 有关更多详细信息，请查看事件中的异常文本。 如果有任何问题，请尝试禁用并重新启用密码写回。 如果这没有帮助，请在提交支持请求时提供事件日志的副本以及指定的跟踪 ID。|
| 32001| ServiceError| 此事件表示连接到云密码重置服务时发生错误。 此错误通常在本地服务无法连接到密码重置 web 服务时发生。|
| 32002| ServiceBusError| 此事件表示连接到租户的服务总线实例时发生错误。 发生此错误的原因可能是在本地环境中阻止了出站连接。 请检查防火墙，确保允许基于 TCP 443 的连接或者到 https://ssprdedicatedsbprodncu.servicebus.windows.net 的连接，并重试。 如果仍然出现问题，请尝试禁用并重新启用密码写回。|
| 32003| InPutValidationError| 此事件表示传递给我们的 web 服务 API 的输入无效。 请重试操作。|
| 32004| DecryptionError| 此事件表示解密从云到达的密码时出错。 这可能是因为云服务与本地环境之间存在解密密钥不匹配问题。 若要解决此问题，请在本地环境中禁用再重新启用密码写回。|
| 32005| ConfigurationError| 在登记期间，我们将特定于租户的信息保存到本地环境中的配置文件中。 此事件表示保存此文件时出错或者启动服务时读取此文件时出错。 若要解决此问题，请尝试禁用并重新启用密码写回以强制重写此配置文件。|
| 32007| OnBoardingConfigUpdateError| 在登记期间，我们将数据从云发送到本地密码重置服务。 然后，在将数据发送到同步服务以安全地在磁盘上存储此信息之前，会将数据写入到一个内存中文件中。 此事件表示在内存中写入或更新该数据时出现问题。 若要解决此问题，请尝试禁用并重新启用密码写回以强制重写此配置文件。|
| 32008| ValidationError| 此事件表示我们从密码重置 web 服务收到了无效的响应。 若要解决此问题，请尝试禁用再重新启用密码写回。|
| 32009| AuthTokenError| 此事件表示我们无法获取在设置 Azure AD Sync 期间指定的全局管理员帐户的授权令牌。 此错误可能是由于为全局管理员帐户指定了错误的用户名或密码导致的。 此错误的另一个可能原因是指定的全局管理员帐户是联合的。 若要解决此问题，请使用正确的用户名和密码重新运行配置，并确保管理员是一个托管帐户（仅云帐户或密码同步的帐户）。|
| 32010| CryptoError| 此事件表示在生成密码加密密钥时或者解密从云服务到达的密码时发生错误。 此错误可能表示环境存在问题。 请查看事件日志的详细信息来了解详细信息并解决此问题。 还可以尝试禁用再重新启用密码写回服务。|
| 32011| OnBoardingServiceError| 此事件表示本地服务无法正确地与密码重置 web 服务进行通信来启动登记过程。 这可能是由于防火墙规则导致的，也可能是因为获取租户的身份验证令牌时出现问题。 若要解决此问题，请确保没有阻止基于 TCP 443 和 TCP 9350-9354 的出站连接或者到 https://ssprdedicatedsbprodncu.servicebus.windows.net 的出站连接。 另请确保用于登记的 Azure AD 管理员帐户不是联合帐户。|
| 32013| OffBoardingError| 此事件表示本地服务无法正确地与密码重置 web 服务进行通信来启动卸载过程。 这可能是由于防火墙规则导致的，也可能是因为获取租户的授权令牌时出现问题。 若要解决此问题，请确保没有阻止基于 443 的出站连接或者到 https://ssprdedicatedsbprodncu.servicebus.windows.net 的出站连接，并确保用于卸载的 Azure Active Directory 管理员帐户不是联合帐户。|
| 32014| ServiceBusWarning| 此事件表示我们必须重新尝试连接到租户的服务总线实例。 正常情况下，这应当无需顾虑，但如果很多次看到此事件，请考虑检查到服务总线的网络连接，特别是当使用高延迟或低带宽连接时。|
| 32015| ReportServiceHealthError| 为了监视密码写回服务的运行状况，我们每五分钟向我们的密码重置 web 服务发送一次检测信号数据。 此事件表示将此运行状况信息发送回云 web 服务时发生错误。 此运行状况信息不包含任何个人数据，是纯粹的检测信号和基本的服务统计信息，因此我们可以在云中提供服务状态信息。|
| 33001| ADUnKnownError| 此事件表示 Active Directory 返回了未知的错误。 有关详细信息，请检查 Azure AD Connect 服务器事件日志中来自 ADSync 源的事件。|
| 33002| ADUserNotFoundError| 此事件表示在本地目录中未找到尝试重置或更改密码的用户。 如果已在本地删除了该用户但在云中未删除，则可能会发生此错误。 如果出现了同步问题，也可能发生此错误。有关详细信息，请查看同步日志以及最近运行的几次同步的详细信息。|
| 33003| ADMutliMatchError| 当密码重置或更改请求来自云时，我们使用在 Azure AD Connect 的设置过程中指定的云定位点来确定如何将该请求链接回本地环境中的用户。 此事件表示我们在本地目录中找到了具有相同的云定位点属性的两个用户。 有关详细信息，请查看同步日志以及最近运行的几次同步的详细信息。|
| 33004| ADPermissionsError| 此事件表示 Active Directory 管理代理 (ADMA) 服务帐户在相关帐户上没有合适的权限来设置新密码。 请确保用户的林中的 ADMA 帐户对林中的所有对象都具有重置密码的权限。 有关如何设置权限的详细信息，请参阅“步骤 4：设置适当的 Active Directory 权限”。 如果用户的属性 AdminCount 设置为 1，也会出现此错误。|
| 33005| ADUserAccountDisabled| 此事件表示我们试图重置或更改在本地已被禁用的帐户的密码。 请启用该帐户，并重试操作。|
| 33006| ADUserAccountLockedOut| 此事件表示我们试图重置或更改在本地已被锁定的帐户的密码。 如果用户在短时间内尝试了太多次更改或重置密码操作，则会发生锁定。 请解锁该帐户并重试操作。|
| 33007| ADUserIncorrectPassword| 此事件表示用户在执行密码更改操作时指定了错误的当前密码。 请指定正确的当前密码，并重试。|
| 33008| ADPasswordPolicyError| 当密码写回服务尝试在本地目录中设置的密码不符合域在密码期限、历史记录、复杂度或筛选方面的要求时，将发生此事件。 <br> <br> 如果使用最短密码期限，并且最近在此时间窗口内已更改过密码，将无法再次更改密码，直到它达到域中指定的期限。 对于测试目的，最短期限应设置为 0。 <br> <br> 如果启用了密码历史记录要求，则必须选择在最近 *N* 次未使用过的密码，其中 *N* 是密码历史记录设置。 如果选择了在最近 *N* 次中使用过的密码，则在此情况下会失败。 对于测试目的，密码历史记录应设置为 0。 <br> <br> 如果有密码复杂性要求，则当用户尝试更改或重置密码时会强制实施所有这些要求。 <br> <br> 如果启用密码筛选器，并且用户选择了不满足筛选条件的密码，则重置或更改操作会失败。|
| 33009| ADConfigurationError| 此事件表示将密码写回到本地目录时由于 Active Directory 存在配置问题而出现问题。 若要详细了解发生了什么错误，请检查 Azure AD Connect 计算机的应用程序事件日志以查找来自 ADSync 服务的消息。|

## <a name="azure-ad-forums"></a>Azure AD 论坛

如果遇到有关 Azure AD 和自助式密码重置的一般性问题，可在 [Azure Active Directory 的 Microsoft 问答页](/answers/topics/azure-active-directory.html)中请求社区帮助。 社区的成员包括工程师、产品经理、MVP 和其他 IT 专业人员。

## <a name="contact-microsoft-support"></a>请与 Microsoft 支持部门联系

如果找不到问题的解答，我们的支持团队始终愿意提供进一步的帮助。

为了能够提供适当的帮助，我们希望你在建立支持案例时提供尽量多的详细信息。 这些详细信息包括：

* **错误的一般描述**：错误是什么？ 看到该错误时出现了哪种行为？ 我们如何再现该错误？ 请尽量提供详尽的信息。
* **页面**：在哪个页面上看到了该错误？ 请附送页面的 URL（如果可以）和屏幕截图。
* **支持代码**：用户看到该错误时生成了哪个支持代码？
   * 若要找到此代码，请再现错误，然后选择屏幕底部的“支持代码”链接，将生成的 GUID 发送给支持工程师。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="使用 GUI 重启 Azure AD Sync 服务":::

  * 如果所在页面的底部没有支持代码，请按 F12，搜索 SID 和 CID，然后将这两个结果发送给支持工程师。
* **日期、时间和时区**：包含发生错误时的确切日期和时间及“时区”。
* **用户 ID**：哪个用户看到了该错误？ 例如，user\@contoso.com。
   * 是否是联合用户？
   * 是否是直通身份验证用户？
   * 是否是密码哈希同步的用户？
   * 是否是仅限云的用户？
* **许可**：用户是否分配有 Azure AD 许可证？
* **应用程序事件日志**：如果你使用的是密码写回，并且错误发生在本地基础结构中，请包含 Azure AD Connect 服务器中的应用程序事件日志的压缩副本。

## <a name="next-steps"></a>后续步骤

若要详细了解 SSPR，请参阅[工作原理：Azure AD 自助式密码重置](concept-sspr-howitworks.md)或 [Azure AD 中的自助式密码重置写回如何工作？](concept-sspr-writeback.md)。
