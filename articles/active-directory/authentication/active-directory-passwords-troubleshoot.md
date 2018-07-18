---
title: 自助密码重置疑难解答 - Azure Active Directory
description: 排查 Azure AD 自助密码重置问题
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 7a23702b40ea46edd6dd139ebdb0a3742193429e
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857212"
---
# <a name="troubleshoot-self-service-password-reset"></a>排查自助密码重置问题

在使用 Azure Active Directory (Azure AD) 自助密码重置 (SSPR) 时遇到了问题？ 下面的信息可帮助你将一切复原。

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>排查用户可能遇到的自助密码重置错误

| 错误 | 详细信息 | 技术详细信息 |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | 抱歉，由于管理员已对组织禁用密码重置，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们启用此功能。 若要了解详细信息，请参阅[请为我提供帮助，我忘记了 Azure AD 密码](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions)。 | SSPR_0009：我们检测到管理员尚未启用密码重置。 请联系管理员，并请求他们为组织启用密码重置。 |
| WritebackNotEnabled = 10 |抱歉，由于管理员没有为组织启用必要服务，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们检查你组织的配置。 若要了解此必要服务的详细信息，请参阅[配置密码写回](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configure-password-writeback)。 | SSPR_0010：我们检测到尚未启用密码写回。 请联系管理员，并请求他们启用密码写回。 |
| SsprNotEnabledInUserPolicy = 11 | 抱歉，由于管理员未为组织配置密码重置，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们配置密码重置。 若要了解有关密码重置配置的详细信息，请参阅[快速入门：Azure AD 自助密码重置](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)。 | SSPR_0011：你的组织尚未定义密码重置策略。 请联系管理员，并请求他们定义密码重置策略。 |
| UserNotLicensed = 12 | 抱歉，由于组织缺少必要的许可证，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们检查许可证分配。 若要了解有关许可的详细信息，请参阅 [Azure AD 自助密码重置的许可要求](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing)。 | SSPR_0012：你的组织没有执行密码重置所必需的许可证。 请联系管理员，并请求他们查阅许可证分配。 |
| UserNotMemberOfScopedAccessGroup = 13 | 抱歉，由于管理员未将帐户配置为使用密码重置，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们配置你的帐户以进行密码重置。 若要了解有关帐户配置以进行密码重置的详细信息，请参阅[为用户推出密码重置](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices)。 | SSPR_0012：你不是已启用密码重置的组的成员。 请联系管理员，并请求添加到组。 |
| UserNotProperlyConfigured = 14 | 抱歉，由于帐户缺少必要信息，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们为你重置密码。 有权重新访问帐户后，需要注册必要的信息。 若要注册信息，请遵循[注册自助密码重置](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register)一文中的步骤。 | SSPR_0014：需要额外的安全信息，才可重置密码。 若要继续，请联系管理员，并请求他们重置你的密码。 有权访问帐户后，可前往 https://aka.ms/ssprsetup 注册额外安全信息。 管理员可按照[为密码重置设置和读取身份验证数据](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell)中所述的步骤，向帐户添加额外安全信息。 |
| OnPremisesAdminActionRequired = 29 | 抱歉，由于贵组织的密码重置配置出现问题，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们进行调查。 若要了解有关潜在问题的详细信息，请参阅[排查密码写回问题](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)。 | SSPR_0029：由于本地配置中的问题，我们无法重置你的密码。 请联系管理员，并请求他们进行调查。 |
| OnPremisesConnectivityError = 30 | 抱歉，由于组织的连接问题，目前无法重置密码。 当前没有可执行的操作，但如果稍后重新尝试，问题有可能已得到解决。 如果问题仍然存在，请联系管理员，并请求他们进行调查。 若要了解有关连接问题的详细信息，请参阅[排查密码写回连接问题](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)。 | SSPR_0030：由于本地环境连接不佳，无法重置密码。 请联系管理员，并请求他们进行调查。|


## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>在 Azure 门户中排查密码重置配置问题

| 错误 | 解决方案 |
| --- | --- |
| Azure 门户中的“Azure AD”下面未显示“密码重置”部分。 | 如果未向执行该操作的管理员分配 Azure AD Premium 或 Basic 许可证，可能会发生这种情况。 <br> <br> 请向相关的管理员帐户分配许可证。 可以遵循[分配、验证和解决许可证问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)一文中的步骤。|
| 未看到某个特定的配置选项。 | UI 的许多元素都是隐藏的，直到需要它们时才会显示。 如果希望看到它们，请尝试启用所有选项。 |
| 未看到“本地集成”选项卡。 | 仅已下载 Azure AD Connect 并配置了密码写回时，此选项才可见。 有关详细信息，请参阅[通过快速设置开始使用 Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md)。 |

## <a name="troubleshoot-password-reset-reporting"></a>排查密码重置报告问题

| 错误 | 解决方案 |
| --- | --- |
| “自助密码管理”审核事件类别中未显示任何密码管理活动类型。 | 如果未向执行该操作的管理员分配 Azure AD Premium 或 Basic 许可证，可能会发生这种情况。 <br> <br> 可以通过向相关管理员帐户分配许可证解决此问题。 请遵循[分配、验证和解决许可证问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)一文中的步骤。 |
| 用户注册显示了多次。 | 目前，当用户注册时，我们会将所注册的每个单独的数据片记录为一个单独的事件。 <br> <br> 如果希望聚合此数据并更灵活地查看此数据，可以下载报告并在 excel 中作为数据透视表打开数据。

## <a name="troubleshoot-the-password-reset-registration-portal"></a>排查密码重置注册门户问题

| 错误 | 解决方案 |
| --- | --- |
| 未针对密码重置启用目录。 **管理员没有允许使用此功能。** | 将“自助密码重置已启用”标志切换为“选定”或“全部”，并选择“保存”。 |
| 未为用户分配 Azure AD Premium 或 Basic 许可证。 **管理员没有允许使用此功能。** | 如果未向执行该操作的管理员分配 Azure AD Premium 或 Basic 许可证，可能会发生这种情况。 <br> <br> 可以通过向相关管理员帐户分配许可证解决此问题。 请遵循[分配、验证和解决许可证问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)一文中的步骤。|
| 处理请求时出错。 | 许多问题都可能会导致此错误，但此错误通常是由服务中断或者配置问题导致的。 如果看到了此错误并且它影响了业务，请与 Microsoft 支持人员联系，以获得更多的帮助。 |

## <a name="troubleshoot-the-password-reset-portal"></a>排查密码重置门户问题

| 错误 | 解决方案 |
| --- | --- |
| 未针对密码重置启用目录。 | 将“自助密码重置已启用”标志切换为“选定”或“全部”，并选择“保存”。 |
| 未为用户分配 Azure AD Premium 或 Basic 许可证。 | 如果未向执行该操作的管理员分配 Azure AD Premium 或 Basic 许可证，可能会发生这种情况。 <br> <br> 可以通过向相关管理员帐户分配许可证解决此问题。 请遵循[分配、验证和解决许可证问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)一文中的步骤。 |
| 已针对密码重置启用了目录，但用户的身份验证信息缺失或格式错误。 | 在继续之前，请确保用户在目录下的文件中有格式正确的联系人数据。 有关详细信息，请参阅 [Azure AD 自助密码重置使用的数据](howto-sspr-authenticationdata.md)。 |
| 已针对密码重置启用目录，但用户在文件中只有一个联系人数据片段，而策略要求执行两种验证方法。 | 在继续之前，请确保用户至少有两个正确配置的联系方法。 例如，同时提供了移动电话号码和办公电话号码。 |
| 已针对密码重置启用目录并正确配置用户，但无法联系到用户。 | 这可能是因临时服务错误或错误的联系人数据导致的，我们无法正确检测到这些错误。 <br> <br> 如果用户等待 10 秒，将出现“重试”和“联系管理员”链接。 如果用户选择“重试”，则会重试呼叫。 如果用户选择“联系管理员”，则向管理员发送表单电子邮件，请求为该用户帐户执行密码重置。 |
| 用户一直收不到密码重置短信或电话呼叫。 | 这可能是由于目录中的电话号码格式不正确导致的。 请确保电话号码的格式为“+ccc xxxyyyzzzzXeeee”。 <br> <br> 密码重置不支持分机，即使你在目录中指定了分机。 分机号码在呼叫前被剥离。 使用不带分机的号码，或者在专用交换机 (PBX) 中将分机集成到电话号码中。 |
| 用户一直收不到密码重置电子邮件。 | 此问题的最常见原因是消息被垃圾邮件筛选器拒绝。 请检查垃圾邮件或已删除邮件文件夹中是否有电子邮件。 <br> <br> 另请确保在正确的电子邮箱帐户中检查是否有该邮件。 |
| 我已经设置了密码重置策略，但当管理员帐户使用密码重置时，该策略未应用。 | 为确保最高级别的安全性，管理员密码重置策略由 Microsoft 进行管理和控制。 |
| 一天中阻止用户尝试密码重置的次数太多。 | 我们实施了一种自动扼制机制来阻止用户在短时间内过多地尝试重置其密码。 如果存在以下情况，将发生限制： <br><ul><li>用户在一小时内 5 次尝试验证某个电话号码。</li><li>用户在一小时内 5 次尝试使用安全问题入口。</li><li>用户在一小时内 5 次尝试为同一用户帐户重置密码。</li></ul>若要解决此问题，请指示用户在最后一次尝试后等待 24 小时。 然后用户即可重置其密码。 |
| 用户在验证其电话号码时看到了一个错误。 | 当输入的电话号码与文件上的电话号码不匹配时，将发生此错误。 当尝试使用基于电话的方法进行密码重置时，请确保用户输入了完整的电话号码（包括区域和国家/地区代码）。 |
| 处理请求时出错。 | 许多问题都可能会导致此错误，但此错误通常是由服务中断或者配置问题导致的。 如果看到了此错误并且它影响了业务，请与 Microsoft 支持人员联系，以获得更多的帮助。 |
| 本地策略冲突 | 该密码不符合本地 Active Directory 密码策略。 |
| 密码不符合模糊策略 | 已使用的密码显示在禁止密码列表中，且可能无法使用。 |

## <a name="troubleshoot-password-writeback"></a>排查密码写回问题

| 错误 | 解决方案 |
| --- | --- |
| 密码重置服务无法在本地启动。 Azure AD Connect 计算机的应用程序事件日志中出现错误 6800。 <br> <br> 加入后，联合身份验证、直通身份验证或密码哈希同步的用户无法重置其密码。 | 当启用了密码写回时，同步引擎将调用写回库通过与云登记服务进行通信来执行配置（登记）。 在登记期间或者为密码写回启动 Windows Communication Foundation (WCF) 终结点时遇到任何错误都将导致在 Azure AD Connect 计算机的事件日志中生成错误。 <br> <br> 在重启 Azure AD Sync (ADSync) 服务期间，如果配置了写回，则 WCF 终结点将启动。 但是，如果终结点启动失败，我们将记录事件 6800 并允许同步服务启动。 存在此事件意味着密码写回终结点未启动。 此事件 6800 的事件日志详细信息以及 PasswordResetService 组件生成的事件日志条目将指明终结点无法启动的原因。 请查看这些事件日志错误，如果密码写回仍不能正常工作，请尝试重启 Azure AD Connect。 如果问题仍然存在，请尝试禁用并重新启用密码写回。
| 如果用户尝试重置密码或解锁启用了密码写回功能的帐户，则操作会失败。 <br> <br> 此外，解锁操作发生后，会在 Azure AD Connect 事件日志中看到一个事件，其中包含：“同步引擎返回了一条错误 hr=800700CE，消息=文件名或扩展太长”。 | 查找用于 Azure AD Connect 的 Active Directory 帐户并重置密码，使其包含的字符数不超过 127 个。 然后，从“开始”菜单打开“同步服务”。 浏览到“连接器”并找到“Active Directory 连接器”。 选择它，然后选择“属性”。 浏览到“凭据”页，并输入新密码。 选择“确定”关闭页面。 |
| 在 Azure AD Connect 安装过程的最后一步，看到了一个错误，它指出无法配置密码写回。 <br> <br> Azure AD Connect 应用程序事件日志包含错误 32009，其文本为“获取身份验证令牌时出错”。 | 在以下两种情况下会发生此错误： <br><ul><li>为在 Azure AD Connect 安装过程开始时指定的全局管理员帐户指定了错误的密码。</li><li>试图将联合用户用于在 Azure AD Connect 安装过程开始时指定的全局管理员帐户。</li></ul> 若要解决此问题，请确保未将联合帐户用于在安装过程开始时指定的全局管理员帐户。 另请确保指定的密码正确。 |
| Azure AD Connect 计算机事件日志包含运行 PasswordResetService 时引发的错误 32002。 <br> <br> 错误如下：“连接到 ServiceBus 出错。 令牌提供程序无法提供安全令牌。” | 本地环境无法连接到云中的 Azure 服务总线终结点。 此错误是由于防火墙规则阻止了到特定端口或 web 地址的出站连接导致的。 有关详细信息，请参阅[连接先决条件](./../connect/active-directory-aadconnect-prerequisites.md)。 在更新这些规则后，重新启动 Azure AD Connect 计算机，密码写回应当会再次开始工作。 |
| 工作一段时间后，联合身份验证、直通身份验证或密码哈希同步的用户无法重置其密码。 | 在某些极少见的情况下，当 Azure AD Connect 已重启时，密码写回服务可能无法重启。 在这些情况下，首先，请检查是否已在本地启用了密码写回。 可以使用 Azure AD Connect 向导或 PowerShell 执行检查（请参阅上面的“操作说明”部分）。 如果此功能显示为已启用，请尝试通过 UI 或 PowerShell 再次启用或禁用此功能。 如果这不起作用，请尝试完全卸载并重新安装 Azure AD Connect。 |
| 尝试重置其密码的联合身份验证、直通身份验证或密码哈希同步的用户在尝试提交密码后看到错误。 该错误指示存在服务问题。 <br ><br> 除此问题以外，在密码重置期间，可能会在本地事件日志中看到有关管理代理被拒绝访问的消息。 | 如果在事件日志中看到这些错误，请确认 Active Directory 管理代理 (ADMA) 帐户（在配置时在向导中指定的帐户）具有进行密码写回所需的权限。 <br> <br> 在授予此权限后，权限可能需要最多一小时来通过域控制器 (DC) 上的 `sdprop` 后台任务进行渗透。 <br> <br> 要使密码重置工作，需要将此权限标记在为其重置密码的用户对象的安全描述符上。 在此权限显示在用户对象上之前，密码重置将持续失败并出现“访问被拒绝”消息。 |
| 尝试重置其密码的联合身份验证、直通身份验证或密码哈希同步的用户在提交密码后看到错误。 该错误指示存在服务问题。 <br> <br> 除此问题以外，在密码重置期间，可能会在 Azure AD Connect 服务的事件日志中看到一个表示“找不到对象”错误的错误。 | 此错误通常表示同步引擎无法找到 Azure AD 连接器空间中的用户对象或者无法找到链接的 Metaverse (MV) 或 Azure AD 连接器空间对象。 <br> <br> 若要解决此问题，请确保用户确实已通过当前的 Azure AD Connect 实例从本地同步到了 Azure AD，并检查连接器空间和 MV 中的对象的状态。 通过“Microsoft.InfromADUserAccountEnabled.xxx”规则确认 Active Directory 证书服务 (AD CS) 对象是 MV 对象的连接器。|
| 尝试重置其密码的联合身份验证、直通身份验证或密码哈希同步的用户在提交密码后看到错误。 该错误指示存在服务问题。 <br> <br> 除此问题以外，在密码重置期间，可能会在 Azure AD Connect 服务的事件日志中看到一个错误，指出发生“找到多个匹配项”错误。 | 这表示同步引擎通过“Microsoft.InfromADUserAccountEnabled.xxx”检测到 MV 对象连接到了多个 AD CS 对象。 这意味着用户在多个林具有已启用的帐户。 密码写回不支持此方案。 |
| 密码操作因为发生配置错误而失败。 应用程序事件日志包含 Azure AD Connect 错误 6329，文本为：“0x8023061f (操作失败，因为未在此管理代理上启用密码同步)”。 | 如果在启用密码写回功能之后将 Azure AD Connect 配置更改为添加新的 Active Directory 林（或者更改为删除现有林之后再重新添加），则会发生此错误。 在这些最近添加的林中，用户的密码操作会失败。 要解决此问题，请在林配置更改完成后，先禁用密码写回功能，再重新启用它。 |

## <a name="password-writeback-event-log-error-codes"></a>密码写回事件日志错误代码

在排查密码写回问题时，最佳做法是检查 Azure AD Connect 计算机上的该应用程序事件日志。 此事件日志包含来自与密码写回相关的两个源的事件。 PasswordResetService 源描述与密码写回操作相关的操作和问题。 ADSync 源描述与在 Active Directory 环境中设置密码相关的操作和问题。

### <a name="if-the-source-of-the-event-is-adsync"></a>如果事件的源是 ADSync

| 代码 | 名称或消息 | 说明 |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619:“某个限制阻止将密码更改为当前指定的密码。” | 当密码写回服务尝试在本地目录中设置的密码不符合域在密码期限、历史记录、复杂度或筛选方面的要求时，将发生此事件。 <br> <br> 如果使用最短密码期限，并且最近在此时间窗口内已更改过密码，将无法再次更改密码，直到它达到域中指定的期限。 对于测试目的，最短期限应设置为 0。 <br> <br> 如果启用了密码历史记录要求，则必须选择在最近 *N* 次未使用过的密码，其中 *N* 是密码历史记录设置。 如果选择了在最近 *N* 次中使用过的密码，则在此情况下会失败。 对于测试目的，密码历史记录应设置为 0。 <br> <br> 如果有密码复杂性要求，则当用户尝试更改或重置密码时会强制实施所有这些要求。 <br> <br> 如果启用密码筛选器，并且用户选择了不满足筛选条件的密码，则重置或更改操作会失败。 |
| 6329 | MMS(3040): admaexport.cpp(2837)：服务器不包含 LDAP 密码策略控件。 | 如果未在 DC 中启用 LDAP_SERVER_POLICY_HINTS_OID 控件 (1.2.840.113556.1.4.2066)，将会发生此问题。 要使用密码写回功能，必须启用该控件。 为此，必须在 Windows Server 2008（装有最新 SP）或更高版本上运行 DC。 如果 DC 位于 2008（低于 R2）上，则还必须应用修补程序 [KB2386717](http://support.microsoft.com/kb/2386717)。 |
| HR 8023042 | 同步引擎返回了错误：hr = 80230402，消息 = 由于存在使用相同定位点的重复条目，尝试获取对象失败。 | 在多个域中启用同一用户 ID 时会发生此错误。 例如，如果正在同步帐户和资源林，并且每个林中存在并启用了同一个用户 ID，则会发生此错误。 <br> <br> 如果使用了不唯一的定位点属性（如别名或 UPN），并且两个用户共享了这同一个定位点属性，则也可能发生此错误。 <br> <br> 若要解决此问题，请确保域中没有任何重复的用户，并且每个用户使用唯一的定位点属性。 |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>如果事件源是 PasswordResetService

| 代码 | 名称或消息 | 说明 |
| --- | --- | --- |
| 31001 | PasswordResetStart | 此事件表示本地服务检测到从云端发出联合身份验证、直通身份验证或密码哈希同步的用户的密码重置请求。 此事件是每个密码重置写回操作中的第一个事件。 |
| 31002 | PasswordResetSuccess | 此事件表示用户在密码重置操作过程中选择了一个新密码。 我们确定该密码满足企业密码要求。 该密码已成功写回到本地 Active Directory 环境。 |
| 31003 | PasswordResetFail | 此事件表示用户选择了一个密码，并且该密码已成功到达本地环境。 但是，当我们尝试在本地 Active Directory 环境中设置该密码时发生失败。 失败的原因可能包括： <br><ul><li>用户的密码不满足域在期限、历史记录、复杂性或筛选器方面的要求。 若要解决此问题，请创建新密码。</li><li>ADMA 服务帐户没有合适的权限在相关的用户帐户上设置新密码。</li><li>该用户的帐户位于不允许密码设置操作的受保护组中（例如，域管理员或企业管理员组）。</li></ul>|
| 31004 | OnboardingEventStart | 如果为 Azure AD Sync 启用了密码写回，并且我们已开始将组织登记到密码写回 Web 服务，则会发生此事件。 |
| 31005 | OnboardingEventSuccess | 此事件表示加入过程成功，并且密码写回功能已就绪可用。 |
| 31006 | ChangePasswordStart | 此事件表示本地服务检测到从云端发出联合身份验证、直通身份验证或密码哈希同步的用户的密码更改请求。 此事件是每个密码更改写回操作中的第一个事件。 |
| 31007 | ChangePasswordSuccess | 此事件表示用户在密码更改操作过程中选择了一个新密码，我们确定该密码满足公司密码要求，并且该密码已成功写回到本地 Active Directory 环境。 |
| 31008 | ChangePasswordFail | 此事件表示用户选择了一个密码，并且该密码已成功到达本地环境，但当我们尝试在本地 Active Directory 环境中设置该密码时出现错误。 失败的原因可能包括： <br><ul><li>用户的密码不满足域在期限、历史记录、复杂性或筛选器方面的要求。 若要解决此问题，请创建新密码。</li><li>ADMA 服务帐户没有合适的权限在相关的用户帐户上设置新密码。</li><li>该用户的帐户位于不允许密码设置操作的受保护组中（例如，域管理员或企业管理员）。</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | 本地服务检测到管理员代表某用户发出联合身份验证、直通身份验证或密码哈希同步的用户的密码重置请求。 此事件是每个由管理员启动的密码重置写回操作中的第一个事件。 |
| 31010 | ResetUserPasswordByAdminSuccess | 管理员在管理员启动的密码重置操作过程中选择了一个新密码。 我们确定该密码满足企业密码要求。 该密码已成功写回到本地 Active Directory 环境。 |
| 31011 | ResetUserPasswordByAdminFail | 管理员代表用户选择了一个密码。 密码已成功到达本地环境。 但是，当我们尝试在本地 Active Directory 环境中设置该密码时发生失败。 失败的原因可能包括： <br><ul><li>用户的密码不满足域在期限、历史记录、复杂性或筛选器方面的要求。 尝试使用新密码来解决此问题。</li><li>ADMA 服务帐户没有合适的权限在相关的用户帐户上设置新密码。</li><li>该用户的帐户位于不允许密码设置操作的受保护组中（例如，域管理员或企业管理员）。</li></ul>  |
| 31012 | OffboardingEventStart | 如果为 Azure AD Sync 禁用了密码写回，则会发生此事件，它表示我们已开始将组织卸载到密码写回 web 服务。 |
| 31013| OffboardingEventSuccess| 此事件表示脱离过程成功并且已成功地禁用了密码写回功能。 |
| 31014| OffboardingEventFail| 此事件表示卸载过程没有成功。 这可能是由于在配置期间指定的云管理员帐户或本地管理员帐户存在权限错误导致的。 也可能是由于在禁用密码写回时尝试使用了联合云全局管理员导致的。 要解决此问题，请检查管理权限并确保在配置密码写回功能时未使用任何联合帐户。|
| 31015| WriteBackServiceStarted| 此事件表示密码写回服务已成功启动。 该服务已准备好接受来自云的密码管理请求。|
| 31016| WriteBackServiceStopped| 此事件表示密码写回服务已停止。 来自云的任何密码管理请求都不会成功。|
| 31017| AuthTokenSuccess| 此事件表示为启动卸载或登记过程，我们已成功检索到在设置 Azure AD Connect 期间指定的全局管理员的授权令牌。|
| 31018| KeyPairCreationSuccess| 此事件表示已成功创建密码加密密钥。 此密钥用于对从云发送到本地环境的密码进行加密。|
| 32000| UnknownError| 此事件表示在执行密码管理操作期间发生未知的错误。 有关更多详细信息，请查看事件中的异常文本。 如果有任何问题，请尝试禁用并重新启用密码写回。 如果这没有帮助，请将事件日志的副本以及内部指定的跟踪 ID 提供给支持工程师。|
| 32001| ServiceError| 此事件表示连接到云密码重置服务时发生错误。 此错误通常在本地服务无法连接到密码重置 web 服务时发生。|
| 32002| ServiceBusError| 此事件表示连接到租户的服务总线实例时发生错误。 发生此错误的原因可能是在本地环境中阻止了出站连接。 请检查防火墙，确保允许基于 TCP 443 的连接或者到 https://ssprsbprodncu-sb.accesscontrol.windows.net/ 的连接，并重试。 如果仍然出现问题，请尝试禁用并重新启用密码写回。|
| 32003| InPutValidationError| 此事件表示传递给我们的 web 服务 API 的输入无效。 请重试操作。|
| 32004| DecryptionError| 此事件表示解密从云到达的密码时出错。 这可能是因为云服务与本地环境之间存在解密密钥不匹配问题。 若要解决此问题，请在本地环境中禁用再重新启用密码写回。|
| 32005| ConfigurationError| 在登记期间，我们将特定于租户的信息保存到本地环境中的配置文件中。 此事件表示保存此文件时出错或者启动服务时读取此文件时出错。 若要解决此问题，请尝试禁用并重新启用密码写回以强制重写此配置文件。|
| 32007| OnBoardingConfigUpdateError| 在登记期间，我们将数据从云发送到本地密码重置服务。 然后，在将数据发送到同步服务以安全地在磁盘上存储此信息之前，会将数据写入到一个内存中文件中。 此事件表示在内存中写入或更新该数据时出现问题。 若要解决此问题，请尝试禁用并重新启用密码写回以强制重写此配置文件。|
| 32008| ValidationError| 此事件表示我们从密码重置 web 服务收到了无效的响应。 若要解决此问题，请尝试禁用再重新启用密码写回。|
| 32009| AuthTokenError| 此事件表示我们无法获取在设置 Azure AD Sync 期间指定的全局管理员帐户的授权令牌。 此错误可能是由于为全局管理员帐户指定了错误的用户名或密码导致的。 此错误的另一个可能原因是指定的全局管理员帐户是联合的。 若要解决此问题，请使用正确的用户名和密码重新运行配置，并确保管理员是一个托管帐户（仅云帐户或密码同步的帐户）。|
| 32010| CryptoError| 此事件表示在生成密码加密密钥时或者解密从云服务到达的密码时发生错误。 此错误可能表示环境存在问题。 请查看事件日志的详细信息来了解详细信息并解决此问题。 还可以尝试禁用再重新启用密码写回服务。|
| 32011| OnBoardingServiceError| 此事件表示本地服务无法正确地与密码重置 web 服务进行通信来启动登记过程。 这可能是由于防火墙规则导致的，也可能是因为获取租户的授权令牌时出现问题。 若要解决此问题，请确保没有阻止基于 TCP 443 和 TCP 9350-9354 的出站连接或者到 https://ssprsbprodncu-sb.accesscontrol.windows.net/ 的出站连接。 另请确保用于登记的 Azure AD 管理员帐户不是联合帐户。|
| 32013| OffBoardingError| 此事件表示本地服务无法正确地与密码重置 web 服务进行通信来启动卸载过程。 这可能是由于防火墙规则导致的，也可能是因为获取租户的授权令牌时出现问题。 若要解决此问题，请确保没有阻止基于 443 的出站连接或者到 https://ssprsbprodncu-sb.accesscontrol.windows.net/ 的出站连接，并确保用于卸载的 Azure Active Directory 管理员帐户不是联合帐户。|
| 32014| ServiceBusWarning| 此事件表示我们必须重新尝试连接到租户的服务总线实例。 正常情况下，这应当无需顾虑，但如果很多次看到此事件，请考虑检查到服务总线的网络连接，特别是当使用高延迟或低带宽连接时。|
| 32015| ReportServiceHealthError| 为了监视密码写回服务的运行状况，我们每五分钟向我们的密码重置 web 服务发送一次检测信号数据。 此事件表示将此运行状况信息发送回云 web 服务时发生错误。 此运行状况信息不包含对象身份信息 (OII) 或个人身份信息 (PII) 数据，是纯粹的检测信号和基本的服务统计信息，以便我们可以在云中提供服务状态信息。|
| 33001| ADUnKnownError| 此事件表示 Active Directory 返回了未知的错误。 有关详细信息，请检查 Azure AD Connect 服务器事件日志中来自 ADSync 源的事件。|
| 33002| ADUserNotFoundError| 此事件表示在本地目录中未找到尝试重置或更改密码的用户。 如果已在本地删除了该用户但在云中未删除，则可能会发生此错误。 如果出现了同步问题，也可能发生此错误。有关详细信息，请检查同步日志以及最近运行的几次同步的详细信息。|
| 33003| ADMutliMatchError| 当密码重置或更改请求来自云时，我们使用在 Azure AD Connect 的设置过程中指定的云定位点来确定如何将该请求链接回本地环境中的用户。 此事件表示我们在本地目录中找到了具有相同的云定位点属性的两个用户。 有关详细信息，请检查同步日志以及最近运行的几次同步的详细信息。|
| 33004| ADPermissionsError| 此事件表示 Active Directory 管理代理 (ADMA) 服务帐户在相关帐户上没有合适的权限来设置新密码。 请确保用户的林中的 ADMA 帐户对林中的所有对象都具有重置和更改密码的权限。 有关如何设置权限的详细信息，请参阅“步骤 4：设置适当的 Active Directory 权限”。|
| 33005| ADUserAccountDisabled| 此事件表示我们试图重置或更改在本地已被禁用的帐户的密码。 请启用该帐户，并重试操作。|
| 33006| ADUserAccountLockedOut| 此事件表示我们试图重置或更改在本地已被锁定的帐户的密码。 如果用户在短时间内尝试了太多次更改或重置密码操作，则会发生锁定。 请解锁该帐户并重试操作。|
| 33007| ADUserIncorrectPassword| 此事件表示用户在执行密码更改操作时指定了错误的当前密码。 请指定正确的当前密码，并重试。|
| 33008| ADPasswordPolicyError| 当密码写回服务尝试在本地目录中设置的密码不符合域在密码期限、历史记录、复杂度或筛选方面的要求时，将发生此事件。 <br> <br> 如果使用最短密码期限，并且最近在此时间窗口内已更改过密码，将无法再次更改密码，直到它达到域中指定的期限。 对于测试目的，最短期限应设置为 0。 <br> <br> 如果启用了密码历史记录要求，则必须选择在最近 *N* 次未使用过的密码，其中 *N* 是密码历史记录设置。 如果选择了在最近 *N* 次中使用过的密码，则在此情况下会失败。 对于测试目的，密码历史记录应设置为 0。 <br> <br> 如果有密码复杂性要求，则当用户尝试更改或重置密码时会强制实施所有这些要求。 <br> <br> 如果启用密码筛选器，并且用户选择了不满足筛选条件的密码，则重置或更改操作会失败。|
| 33009| ADConfigurationError| 此事件表示将密码写回到本地目录时由于 Active Directory 存在配置问题而出现问题。 有关发生了什么错误的详细信息，请检查 Azure AD Connect 计算机的应用程序事件日志以查找来自 ADSync 服务的消息。|

## <a name="troubleshoot-password-writeback-connectivity"></a>排查密码写回连接问题

如果遇到 Azure AD Connec 密码写回组件的服务中断，可以使用以下快速步骤来解决此问题：

* [确认网络连接](#confirm-network-connectivity)
* [重启 Azure AD Connect Sync 服务](#restart-the-azure-ad-connect-sync-service)
* [禁用再重新启用密码写回功能](#disable-and-re-enable-the-password-writeback-feature)
* [安装最新版本的 Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [排查密码写回问题](#troubleshoot-password-writeback)

一般情况下，若要以最快的方式恢复服务，我们建议按照上述顺序执行这些步骤。

### <a name="confirm-network-connectivity"></a>确认网络连接

最常见的故障点是防火墙和/或代理端口以及空闲超时未正确配置。 

对于 Azure AD Connect 1.1.443.0 和更高版本，需要对以下站点进行出站 HTTPS 访问：

   - passwordreset.microsoftonline.com
   - servicebus.windows.net

若要进行更精细的访问，请参考 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)的更新列表，该列表在每周三更新，在下周一生效。

有关详细信息，请查看 [Azure AD Connect 的先决条件](./../connect/active-directory-aadconnect-prerequisites.md)一文中的连接先决条件。



### <a name="restart-the-azure-ad-connect-sync-service"></a>重启 Azure AD Connect Sync 服务

若要解决连接问题或该服务出现的其他暂时性问题，请重启 Azure AD Connect Sync 服务：

   1. 在运行 Azure AD Connect 的服务器上以管理员身份选择“开始”。
   2. 在搜索字段中输入 **services.msc**，按 **Enter**。
   3. 找到“Microsoft Azure AD Sync”条目。
   4. 右键单击该服务条目，选择“重启”，并等待操作完成。

   ![重新启动 Azure AD Sync 服务][Service restart]

这些步骤重新建立与云服务的连接，并解决可能出现的任何中断。 如果重启 ADSync 服务无法解决所遇到的问题，我们建议尝试禁用密码写回功能，然后重新启用该功能。

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>禁用再重新启用密码写回功能

若要解决连接问题，请禁用再重新启用密码写回功能：

   1. 以管理员身份打开 Azure AD Connect 配置向导。
   2. 在“连接到 Azure AD”中，输入 Azure AD 全局管理员凭据。
   3. 在“连接到 AD DS”中，输入 AD 域服务管理员凭据。
   4. 在“唯一标识用户”中，选择“下一步”按钮。
   5. 在“可选功能”中，清除“密码写回”复选框。
   6. 选择“下一步”完成剩余的对话框页，而不更改任何内容，直到转到“准备好配置”页。
   7. 确保“准备好配置”页上的“密码写回”选项显示为“已禁用”，并选择绿色的“配置”按钮提交所做的更改。
   8. 在“已完成”中，清除“立即同步”选项，并选择“完成”关闭向导。
   9. 重新打开 Azure AD Connect 配置向导。
   10. 重复步骤 2-8，但确保选中“可选功能”页上的“密码写回”选项以重新启用该服务。

这些步骤重新建立与云服务的连接，并解决可能出现的任何中断。

如果禁用再重新启用密码写回功能无法解决你遇到的问题，我们建议重新安装 Azure AD Connect。

### <a name="install-the-latest-azure-ad-connect-release"></a>安装最新版本的 Azure AD Connect

重新安装 Azure AD Connect 可以解决云服务与本地 Active Directory 环境之间的配置和连接问题。

我们建议仅在尝试了上述前两个步骤后再执行此步骤。

> [!WARNING]
> 如果自定义了现成的同步规则，请先备份这些规则，再继续进行升级，并在完成后手动重新部署这些规则。

   1. 从 [Microsoft 下载中心](http://go.microsoft.com/fwlink/?LinkId=615771)下载最新版本的 Azure AD Connect。
   2. 由于已安装 Azure AD Connect，则需要执行就地升级将 Azure AD Connect 安装更新到最新版本。
   3. 执行下载的程序包，并按照屏幕说明进行操作来更新 Azure AD Connect 计算机。

上述步骤应会重新建立与云服务的连接，并解决可能出现的任何中断。

如果安装最新版本的 Azure AD Connect 服务器无法解决所遇到的问题，我们建议在安装最新的版本之后，最后再尝试禁用密码写回，然后重新启用该功能。

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>验证 Azure AD Connect 是否具有密码写回所需的权限

Azure AD Connect 需要 Active Directory“重置密码”权限才能执行密码写回。 若要了解 Azure AD Connect 是否具有给定本地 Active Directory 用户帐户的所需权限，可使用 Windows 有效权限功能：

   1. 登录到 Azure AD Connect 服务器，并选择“开始” > “同步服务”启动“Synchronization Service Manager”。
   2. 在“连接器”选项卡下，选择本地“Active Directory 域服务器”连接器，并选择“属性”。  

   ![有效权限 - 步骤 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
   3. 在弹出窗口中，选择“连接到 Active Directory 林”，并记下“用户名”属性。 此属性是 Azure AD Connect 用于执行目录同步的 AD DS 帐户。 若要让 Azure AD Connect 执行密码写回，AD DS 帐户必须具有重置密码权限。  
   
   ![有效权限 - 步骤 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
   4. 登录到本地域控制器并启动“Active Directory 用户和计算机”应用程序。
   5. 选择“视图”并确保已启用“高级功能”选项。  
   
   ![有效权限 - 步骤 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
   6. 查找要验证的 Active Directory 用户帐户。 右键单击帐户名称并选择“属性”。  
   
   ![有效权限 - 步骤 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 

   7. 在弹出窗口中，转到“安全”选项卡，并选择“高级”。  
   
   ![有效权限 - 步骤 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
   
   8. 在“管理员的高级安全设置”弹出窗口中，转到“有效访问权限”选项卡。
   9. 选择“选择用户”并选择 Azure AD Connect 使用的 AD DS 帐户（请参阅步骤 3），然后选择“查看有效访问权限”。  
   
   ![有效权限 - 步骤 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
   10. 向下滚动并查找“重置密码”。 如果已选中该条目，则表示 AD DS 帐户有权重置所选 Active Directory 用户帐户的密码。  
   
   ![有效权限 - 步骤 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD 论坛

如果遇到 Azure AD 和自助密码重置的一般性问题，可以在 [Azure AD 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD)中请求社区帮助。 社区的成员包括工程师、产品经理、MVP 和其他 IT 专业人员。

## <a name="contact-microsoft-support"></a>请与 Microsoft 支持部门联系

如果找不到问题的解答，我们的支持团队始终愿意提供进一步的帮助。

为了能够提供适当的帮助，我们希望你在建立支持案例时提供尽量多的详细信息。 这些详细信息包括：

* **错误的一般说明**：遇到了哪种错误？ 看到该错误时出现了哪种行为？ 我们如何再现该错误？ 请尽量提供详尽的信息。
* **页面**：在哪个页面上看到了该错误？ 请附送页面的 URL（如果可以）和屏幕截图。
* **支持代码**：用户看到该错误时生成了哪个支持代码？
    * 若要找到此代码，请再现错误，然后选择屏幕底部的“支持代码”链接，将生成的 GUID 发送给支持工程师。

    ![在屏幕底部查找支持代码][Support code]

    * 如果所在页面的底部没有支持代码，请按 F12，搜索 SID 和 CID，然后将这两个结果发送给支持工程师。
* **日期、时间和时区**：请包含发生错误时的确切日期和时间与时区。
* **用户 ID**：哪个用户看到了该错误？ 例如 *user@contoso.com*。
    * 是否是联合用户？
    * 是否是直通身份验证用户？
    * 是否是密码哈希同步的用户？
    * 是否是仅限云的用户？
* **许可**：该用户是否分配有 Azure AD Premium 或 Azure AD Basic 许可证？
* **应用程序事件日志**：如果使用密码写回，并且错误发生在本地基础结构中，请包含 Azure AD Connect 服务器中的应用程序事件日志的压缩副本。



[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "重启 Azure AD Sync 服务"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "支持代码位于窗口右下方"

## <a name="next-steps"></a>后续步骤

以下文章提供了有关通过 Azure AD 进行密码重置的更多信息：

* [如何成功推出 SSPR？](howto-sspr-deployment.md)
* [重置或更改密码](../active-directory-passwords-update-your-own-password.md)
* [注册自助密码重置](../active-directory-passwords-reset-register.md)
* [是否有许可问题？](concept-sspr-licensing.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](howto-sspr-authenticationdata.md)
* [哪些身份验证方法可供用户使用？](concept-sspr-howitworks.md#authentication-methods)
* [SSPR 有哪些策略选项？](concept-sspr-policy.md)
* [什么是密码写回？我为什么关心它？](howto-sspr-writeback.md)
* [如何报告 SSPR 中的活动？](howto-sspr-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](concept-sspr-howitworks.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)
