---
title: "在 Azure Active Directory 中排查企业状态漫游设置问题 | Microsoft 文档"
description: "就 IT 管理员可能会遇到的一些设置和应用数据同步问题提供解答。"
services: active-directory
keywords: "企业状态漫游设置, Windows 云, 企业状态漫游的常见问题解答"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.openlocfilehash: e4977edd19d77b78f08163a6c0ff750fdbefcb3f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>在 Azure Active Directory 中排查企业状态漫游设置问题

本主题介绍了如何排查和诊断企业状态漫游问题，并提供已知问题的列表。

## <a name="preliminary-steps-for-troubleshooting"></a>故障排除预备步骤 
在开始故障排除之前，请确认已正确配置用户和设备，并且设备和用户满足企业状态漫游的所有要求。 

1. 已在设备上安装包含最新更新的 Windows 10，最低版本需为 1511（OS 内部版本 10586 或更高）。 
2. 设备是已加入 Azure AD 的或已加入混合 Azure AD 的。 有关详细信息，请参阅[如何使设备受 Azure AD 控制](device-management-introduction.md)。
3. 确保如[启用企业状态漫游](active-directory-windows-enterprise-state-roaming-enable.md)中所述在 Azure AD 中为租户启用了**企业状态漫游**。 可以为所有用户启用漫游，也可以仅为选定的一组用户启用漫游。
4. 必须已经为用户分配了 Azure Active Directory Premium 许可证。  
25. 必须重启设备，并且用户必须重新登录才能访问企业状态漫游功能。

## <a name="information-to-include-when-you-need-help"></a>需要帮助时应包含的信息
如果根据以下指导仍然无法解决问题，请联系我们的支持工程师。 在联系支持工程师时，请提供以下信息：

* **错误的一般说明**：用户是否看到了错误消息？ 如果没有任何错误消息，请详细描述所发现的意外行为。 为同步启用了哪些功能，用户可同步哪些内容？ 多个功能是否不能同步，或者说只能同步一个功能？
* **受影响的用户** – 同步的成功/失败是针对一个用户还是多个用户？ 每个用户涉及到多少台设备？ 这些设备是否全都不能同步，或者只是同步其中的一部分？
* **关于用户的信息** - 用户登录到设备时使用的是哪个标识？ 用户如何登录到设备？ 他们是否属于可同步的选定安全组？ 
* **关于设备的信息** – 此设备是否已加入 Azure AD 或已加入域？ 设备位于哪个内部版本中？ 有哪些最新的更新？
- **日期/时间/时区** – 看到错误时的准确日期和时间（包括时区）是什么？

包含这些信息有助于我们尽快解决问题。

## <a name="troubleshooting-and-diagnosing-issues"></a>排查和诊断问题
本部分提供有关如何排查和诊断企业状态漫游相关问题的建议。

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>验证同步；“同步设置”设置页 

1. 将 Windows 10 电脑加入到配置为允许企业状态漫游的域之后，请使用工作帐户登录。 转到“设置” > “帐户” > “同步设置”，确认同步和各项设置已打开，并且设置页的顶部指示将与工作帐户同步。 在“设置” > “帐户” > “信息”中确认同一个帐户也用作登录帐户。 
2. 在原始计算机上进行一些更改（例如，将任务栏移到屏幕右侧或顶部），验证同步是否能够跨多台计算机正常工作。 观察更改能否在 5 分钟内传播到第二台计算机。 
  * 可以借助锁定和解锁屏幕 (Win + L) 来触发同步。
  * 必须在这两台 PC 上使用同一帐户登录，同步才能工作 - 因为企业状态漫游绑定到用户帐户，而不是计算机帐户。

**潜在问题**：如果“设置”页中的控件不可用，并且看到了消息“仅当你使用的是 Microsoft 帐户或工作帐户时，某些 Windows 功能才可用。” 对于设置为要加入域并注册到 Azure AD 但尚未在 Azure AD 中成功进行身份验证的设备，可能会出现此问题。 一个可能的原因是必须应用设备策略，但这种策略应用是异步发生的，可能会延迟几个小时。 

### <a name="verify-the-device-registration-status"></a>验证设备注册状态
企业状态漫游要求将设备注册到 Azure AD。 以下说明尽管不专门针对企业状态漫游，但可帮助确认 Windows 10 客户端是否已注册，并确认指纹、Azure AD 设置 URL、NGC 状态和其他信息。

1.  打开未提升权限的命令提示符。 若要在 Windows 中执行此操作，请打开“运行”启动器 (Win + R) 并键入“cmd”。
2.  打开命令提示符后，键入“*dsregcmd.exe /status*”。
3.  在预期的输出中，**AzureAdJoined** 字段值应为“YES”，**WamDefaultSet** 字段值应为“YES”，**WamDefaultGUID** 字段值应是末尾为“(AzureAd)”的 GUID。

**潜在问题**：**WamDefaultSet** 和 **AzureAdJoined** 的字段值中均包含“NO”，设备已加入域并已注册到 Azure AD，但设备不同步。如果显示这样的信息，则表示设备可能需要等待应用策略，或者在连接到 Azure AD 时设备身份验证失败。 用户可能需要等待几个小时来应用策略。 其他故障排除步骤可能包括通过注销再重新登录，或者在任务计划程序中启动任务，来重试自动注册。 某些情况下，在权限提升的命令提示窗口中运行“*dsregcmd.exe /leave*”，重新启动，并重试注册，可能有助于解决此问题。


**潜在问题**：**AzureAdSettingsUrl** 的字段为空，设备不同步。在 Azure Active Directory 门户中启用企业状态漫游之前，用户可能已登录到设备。 重启设备并让用户登录。 （可选）在门户中，尝试让 IT 管理员先禁用再重启“用户可同步设置”和“企业应用数据”。 重新启用后，重新启动设备并让用户登录。 如果这未解决该问题，则在设备证书错误的情况下，**AzureAdSettingsUrl** 可能为空。 在此情况下，在权限提升的命令提示符窗口中运行“*dsregcmd.exe /leave*”，重启然后重试注册，可能有助于解决此问题。

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>企业状态漫游和多重身份验证 
在某些情况下，如果未配置 Azure 多重身份验证，企业状态漫游可能无法同步数据。 有关这些症状的详细信息，请参阅支持文档 [KB3193683](https://support.microsoft.com/kb/3193683)。 

**潜在问题**：如果在 Azure Active Directory 门户中将设备配置为需要多重身份验证，则在使用密码登录到 Windows 10 设备时可能无法对设置进行同步。 这种多重身份验证配置旨在保护 Azure 管理员帐户。 通过 Microsoft Passport for Work PIN 或通过访问其他 Azure 服务（如 Office 365）时完成多重身份验证，管理员用户仍能登录到 Windows 10 设备，从而进行同步。

**潜在问题**：如果管理员配置 Active Directory 联合身份验证服务多重身份验证条件访问策略，且设备上的访问令牌过期，那么同步可能会失败。 确保使用 Microsoft Passport for Work PIN 进行登录和注销，或在访问其他 Azure 服务（如 Office 365）时完成多重身份验证。

###<a name="event-viewer"></a>事件查看器
要进行高级故障排除，可以使用事件查看器查找特定的错误。 下表中介绍了如何查找。 在事件查看器的“应用程序和服务日志”>“Microsoft” > “Windows” > “SettingSync”下面可以查找事件；在“Microsoft” > “Windows” > “Azure AD”下面可以查找同步标识相关的问题。


## <a name="known-issues"></a>已知问题

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>在使用 MDM 软件加载了应用端的设备上无法同步

会影响到运行 Windows 10 周年更新（版本 1607）的设备。 在事件查看器中的 SettingSync-Azure 日志下面，经常会出现事件 ID 6013 和错误 80070259。

**建议的操作**  
确保在 Windows 10 v1607 客户端上安装 2016 年 8 月 23 日累积更新（[KB3176934](https://support.microsoft.com/kb/3176934) OS 内部版本 14393.82）。 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Internet Explorer 收藏夹不同步

会影响到运行 Windows 10 十一月更新（版本 1511）的设备。

**建议的操作**  
确保在 Windows 10 v1511 客户端上安装 2016 年 7 月累积更新（[KB3172985](https://support.microsoft.com/kb/3172985) OS 内部版本 10586.494）。

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>主题不同步，受 Windows 信息保护功能保护的数据也不同步 

在使用 Windows 10 周年更新的设备上，为防止数据泄漏，不会通过企业状态漫游同步受 [Windows 信息保护](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip)功能保护的数据。



**建议的操作**  
无。 Windows 的未来更新可能会解决此问题。

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>在已加入域的设备上，“日期、时间和地区”设置不同步 
  
在已加入域的设备上使用自动时间时，“日期、时间和地区”设置不同步。 使用自动时间可能会覆盖其他“日期、时间和地区”设置，导致这些设置不同步。 

**建议的操作**  
无。 

---

### <a name="uac-prompts-when-syncing-passwords"></a>同步密码时出现 UAC 提示

会影响到运行 Windows 10 十一月更新（版本 1511）且包含配置为同步密码的无线 NIC 的设备。

**建议的操作**  
确保在 Windows 10 v1511 客户端上安装累积更新（[KB3140743](https://support.microsoft.com/kb/3140743) OS 内部版本 10586.494）。

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>在使用智能卡登录的设备上无法同步
如果尝试使用智能卡或虚拟智能卡登录到 Windows 设备，设置同步会停止工作。     

**建议的操作**  
无。 Windows 的未来更新可能会解决此问题。

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>已加入域的设备在脱离企业网络后不同步     
如果已加入域并已注册到 Azure AD 的设备长时间离线并且域身份验证无法完成，则可能会发生同步失败。

**建议的操作**  
将设备连接到企业网络即可恢复同步。

---

 ### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>已加入 Azure AD 的设备不同步，且用户具有混合大小写的用户主体名称。
 如果用户的 UPN 混用大小写（例如 UserName 而非 username），并且该用户位于已从 Windows 10 内部版本 10586 升级到 14393 的 Azure AD 联接设备上，则用户的设备可能无法同步。 

**建议的操作**  
用户将需要断开联接并将设备重新加入到云中。 为此，请以本地管理员用户身份登录，并通过转到“设置” > “系统” > “关于”使设备脱离，再选择“从工作或学校管理或断开”。 清理以下文件，Azure AD 将通过以下方式再次联接设备：转到“设置” > “系统” > “关于”，并选择“连接到工作或学校”。 继续将设备加入到 Azure Active Directory 并完成该流。

在清理步骤中，清除以下文件：
- `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\` 中的 settings.dat
- `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account` 文件夹下的所有文件

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>事件 ID 6065: 80070533 此用户无法登录，因为此帐户当前已禁用  
如果用户凭据过期，则事件查看器的 SettingSync/Debug 日志下可能会出现此错误。 此外，如果租户未自动预配 AzureRMS，也可能会出现此错误。 

**建议的操作**  
在第一种情况下，让用户更新其凭据并使用新凭据登录到设备。 若要解决 AzureRMS 问题，请继续执行 [KB3193791](https://support.microsoft.com/kb/3193791) 中列出的步骤。 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>事件 ID 1098: 错误: 0xCAA5001C 令牌代理操作失败  
在事件查看器中的 AAD/Operational 日志下面可能会出现此错误，其中附带消息：“事件 1104: AAD 云 AP 插件调用 Get 令牌返回了错误: 0xC000005F”。 如果缺少权限或所有权属性，将出现此问题。  

**建议的操作**  
执行 [KB3196528](https://support.microsoft.com/kb/3196528) 中所列的步骤。  



## <a name="next-steps"></a>后续步骤

- 欢迎在[用户之声论坛](https://feedback.azure.com/forums/169401-azure-active-directory/category/158658-enterprise-state-roaming)中提供反馈，并提出有关如何改进企业状态漫游的建议。

- 有关详细信息，请参阅[企业状态漫游概述](active-directory-windows-enterprise-state-roaming-overview.md)。 

## <a name="related-topics"></a>相关主题
* [企业状态漫游概述](active-directory-windows-enterprise-state-roaming-overview.md)
* [在 Azure Active Directory 中启用企业状态漫游](active-directory-windows-enterprise-state-roaming-enable.md)
* [设置和数据漫游的常见问题](active-directory-windows-enterprise-state-roaming-faqs.md)
* [设置同步的组策略和 MDM 设置](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 漫游设置参考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
