---
title: "安全电子邮件建议的策略 | Microsoft Docs"
description: "介绍了 Microsoft 在如何应用电子邮件策略和配置方面的建议。"
author: jeffgilb
manager: femila
editor: jsnow
ms.service: guidance
ms.topic: article
ms.date: 07/12/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
pnp.series.title: Best Practices
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 1d9577f44adcdefa0b0b8f0f2bafce8d0bda4697
ms.contentlocale: zh-cn
ms.lasthandoff: 07/15/2017

---

# <a name="recommended-policies"></a>建议的策略
 
本文介绍了用于帮助客户保护组织的电子邮件安全以及电子邮件客户端支持新式验证和条件访问的建议策略。 它还讨论了我们建议使用的可以为用户提供最佳 SSO 体验的默认平台客户端配置，并介绍了进行条件访问的技术先决条件。

## <a name="prerequisites"></a>先决条件

在实现本文档剩余部分中所述的策略之前，你的组织必须满足若干先决条件：
* [配置已命名网络](https://docs.microsoft.com/azure/active-directory/active-directory-known-networks-azure-portal)。 Azure AD Identity Protection 收集并分析所有可用的会话数据来生成风险分数。 建议在 Azure AD 已命名网络配置中为你的网络指定你的组织的公共 IP 地址。 来自这些范围的流量会被给予一个较低的风险分数，因此，来自公司环境外部的流量将被认为具有较高的风险分数。
* [要求所有用户注册多重身份验证 (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-manage-users-and-devices)。 Azure AD Identity Protection 使用 Azure MFA 执行额外的安全验证。 建议你要求所有用户提前注册 Azure MFA。
* [为加入域的 Windows 计算机启用自动设备注册](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)。 条件访问可以确保连接到服务的设备是加入域的设备或兼容设备。 若要在 Windows 计算机上支持此功能，必须将设备注册到 Azure AD。  本文讨论了如何配置自动化设备注册。  请注意，AD FS 是必需项。
* **准备你的支持团队**。 针对无法完成 MFA 的用户制定计划。 此计划可以是将它们添加到一个策略排除组，或者为它们注册新的 MFA 信息。 在进行这些安全敏感的更改之前，需要确保实际用户正在发出请求。 要求用户的经理帮助进行批准是一个有效的步骤。
* [配置向本地 AD 的密码写回](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)。 密码写回允许 Azure AD 在检测到高风险帐户危害时要求用户更改其本地密码。 可以通过两种方式之一使用 Azure AD Connect 启用此功能。 可以在 Azure AD Connect 设置向导的可选功能屏幕中启用密码写回，也可以通过 Windows PowerShell 启用。  
* [启用新式验证](https://support.office.com/article/Enable-Exchange-Online-for-modern-authentication-58018196-f918-49cd-8238-56f57f38d662)和[保护旧终结点](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-supported-apps)。  条件性访问可与使用新式身份验证的移动和桌面应用程序搭配使用。 如果应用程序使用旧式身份验证协议，则无论在什么条件下，它都可以获得访问权限。 请务必了解哪些应用程序可以使用条件访问规则，以及保护其他应用程序入口点需要采取的步骤。
* 通过激活权限管理[启用 Azure 信息保护](https://docs.microsoft.com/information-protection/get-started/infoprotect-tutorial-step1)。 将 Azure 信息保护用于电子邮件以开始电子邮件分类。 按照快速入门教程自定义并发布策略。  

> [!NOTE]
> 下面的建议基于可以根据你的需求粒度针对电子邮件应用的三个不同的安全和保护层次：[基线、敏感和高度管制](https://docs.microsoft.com/azure/active-directory/secure-email-introduction)。 可以通过下载此 [Office 365 的标识和设备保护信息图](https://go.microsoft.com/fwlink/p/?linkid=841656)来了解更多信息。  


## <a name="baseline"></a>基线 
本部分介绍了针对基线层次的数据、标识和设备保护提供的安全电子邮件建议。 这些建议应当能够满足许多组织的默认保护需求。
>[!NOTE]
>下面的策略是累加的，并且是在彼此的基础上构建的。 每部分仅介绍了应用于每个层次的累加项。
>

### <a name="conditional-access-policy-settings"></a>条件访问策略设置

#### <a name="identity-protection"></a>标识保护 
可以如前面的部分所述为用户提供单一登录 (SSO) 体验。 你只需要根据[风险事件](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)在必要时进行干预。  

* 要求针对中等或更高的登录风险进行 MFA
* 针对高风险用户要求进行安全的密码更改

>[!IMPORTANT]
>对于此策略建议，[密码同步](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)和[自助密码重置](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)是必需的。
>

#### <a name="data-loss-prevention"></a>数据丢失预防 
设备和应用管理策略的目标是防止在设备丢失或被盗的情况下丢失数据。 可以通过以下方式实现此目标：确保通过 PIN 保护对数据的访问、确保数据在设备上处于加密状态、确保设备不受侵害。

|策略建议|说明|
|:--------------------|:----------|
|**要求实现用户 PC 管理**|要求用户将其 PC 加入某个 Active Directory 域或者将其 PC 注册到 Intune 或 Configuration Manager 中的管理功能|
|**通过组策略对象 (GPO) 或 Configuration Manager 策略为加入域的 PC 应用安全设置**|部署策略来对托管 PC 进行配置以启用 BitLocker、启用防病毒软件以及启用防火墙|
|**要求实现用户移动设备管理**|要求用来访问电子邮件的用户设备由 Intune 进行管理或者只能通过受 Intune 应用保护策略保护的移动电子邮件应用（例如 Outlook Mobile）访问公司电子邮件|
|**在托管设备上应用 Intune 设备符合性策略**|对托管的公司移动设备和 Intune 管理的 PC 应用 Intune 设备符合性策略，该策略要求具有：长度最少为 6 的 PIN、设备加密、正常运行的设备（未越狱、未 root；通过了运行状况证明）；并且，如果可行，还要求设备经第三方 MTP（例如 Lookout 或 SkyCure）确定为低风险的。|
|**对在非托管设备上运行的托管应用应用 Intune 应用保护策略**|对在非托管的个人移动设备上运行的托管应用应用 Intune 应用保护策略来要求具有长度最少为 6 的 PIN、设备加密，并要求设备为正常运行的（未越狱、未 root；通过了运行状况证明）。|

### <a name="user-impact"></a>用户影响

对于大多数组织，能够针对期望用户在何时以及在什么情况下将登录 Office 365 来访问其电子邮件而设定用户期望非常重要。  

用户通常可以从单一登录 (SSO) 受益，但在下列情况下除外： 
* 请求 Exchange Online 的身份验证令牌时：
  * 每当检测到中等或更高级别的登录风险且用户在其当前会话中尚未执行 MFA 时，都可能会要求用户执行 MFA。  
  * 将要求用户使用支持 Intune 应用保护 SDK 的电子邮件应用或要求其从符合 Intune 的或已加入 AD 域的设备访问电子邮件。 
* 当处于风险中的用户登录并成功完成了 MFA 时，会要求他们更改其密码。

## <a name="sensitive"></a>敏感

本部分介绍了针对敏感层次的数据、标识和设备保护提供的安全电子邮件建议。 这些建议适用于其所有或部分数据必须在较高级别进行保护的客户。 

可以对 Office 365 环境中的所有或特定数据集应用强化保护。 例如，可以应用策略来确保仅在受保护的应用之间共享敏感数据以防止数据丢失。 建议通过相应的安全级别对访问敏感数据的标识和设备进行保护。 

### <a name="conditional-access-policy-settings"></a>条件访问策略设置
#### <a name="identity-protection"></a>标识保护 

可以如前面的部分所述为用户提供单一登录 (SSO) 体验。 你只需要根据[风险事件](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)在必要时进行干预。   

* 要求针对低风险或更高风险会话进行 MFA
 * 针对高风险用户要求进行安全的密码更改

>[!IMPORTANT]
>对于此策略建议，[密码同步](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)和[自助密码重置](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)是必需的。
>

#### <a name="data-loss-prevention"></a>数据丢失预防 

设备和应用管理策略的目标是防止在设备丢失或被盗的情况下丢失数据。 可以通过以下方式实现此目标：确保通过 PIN 保护对数据的访问、确保数据在设备上处于加密状态、确保设备不受侵害。

|策略建议|说明|
|:--------------------|:----------|
|**要求实现用户 PC 管理**|要求用户将其 PC 加入某个 Active Directory 域或者将其 PC 注册到 Intune 或 Configuration Manager 中的管理功能并且在允许电子邮件访问之前确保这些设备符合策略|
|**通过组策略对象 (GPO) 或 Configuration Manager 策略为加入域的 PC 应用安全设置**|部署策略来对托管 PC 进行配置以启用 BitLocker、启用防病毒软件以及启用防火墙|
|**要求实现用户移动设备管理**|要求用来访问电子邮件的用户设备由 Intune 进行管理或者只能通过受 Intune 应用保护策略保护的移动电子邮件应用（例如 Outlook Mobile）访问公司电子邮件|
|**在托管设备上应用 Intune 设备符合性策略**|对托管的公司移动设备和 Intune 管理的 PC 应用 Intune 设备符合性策略，该策略要求具有：长度最少为 6 的 PIN、设备加密、正常运行的设备（未越狱、未 root；通过了运行状况证明）；并且，如果可行，还要求设备经第三方 MTP（例如 Lookout 或 SkyCure）确定为低风险的。|
|**对在非托管设备上运行的托管应用应用 Intune 应用保护策略**|对在非托管的个人移动设备上运行的托管应用应用 Intune 应用保护策略来要求具有长度最少为 6 的 PIN、设备加密，并要求设备为正常运行的（未越狱、未 root；通过了运行状况证明）。|

### <a name="user-impact"></a>用户影响

对于大多数组织，能够针对期望用户在何时以及在什么情况下将登录到 Office 365 电子邮件而设定用户期望非常重要。 

用户通常可以从单一登录 (SSO) 受益，但在下列情况下除外： 
* 请求 Exchange Online 的身份验证令牌时：
  * 每当检测到低风险或更高级别的登录风险且用户在其当前会话中尚未执行 MFA 时，都会要求用户执行 MFA。  
  * 将要求用户使用支持 Intune 应用保护 SDK 的电子邮件应用或要求其从符合 Intune 的或已加入 AD 域的设备访问电子邮件。 
* 当处于风险中的用户登录并成功完成了 MFA 时，会要求他们更改其密码。

## <a name="highly-regulated"></a>高度管制
本部分介绍了针对高度管制层次的数据、标识和设备保护提供的安全电子邮件建议。 这些建议适用于其数据中有很小一部分数据是高度机密、商业机密或高度管制的数据的客户。 Microsoft 会提供相应功能来帮助组织满足这些要求，包括对标识和设备的增强保护。 

### <a name="conditional-access-policy-settings"></a>条件访问策略设置
#### <a name="identity-protection"></a>标识保护 

对于高度管制层次，Microsoft 建议针对所有新会话强制实施 MFA。
* 要求针对所有新会话进行 MFA
* 针对高风险用户要求进行安全的密码更改

>[!IMPORTANT]
>对于此策略建议，[密码同步](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)和[自助密码重置](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)是必需的。
>

#### <a name="data-loss-prevention"></a>数据丢失预防
设备和应用管理策略的目标是防止在设备丢失或被盗的情况下丢失数据。 可以通过以下方式实现此目标：确保通过 PIN 保护对数据的访问、确保数据在设备上处于加密状态、确保设备不受侵害。

对于高度管制层次，我们建议要求仅在符合 Intune 的或已加入域的设备上运行支持 Intune 应用保护策略的应用。

|策略建议|说明|
|:--------------------|:----------|
|**要求实现用户 PC 管理**|要求用户将其 PC 加入某个 Active Directory 域或者将其 PC 注册到 Intune 或 Configuration Manager 中的管理功能并且在允许电子邮件访问之前确保这些设备符合策略|
|**通过组策略对象 (GPO) 或 Configuration Manager 策略为加入域的 PC 应用安全设置**|部署策略来对托管 PC 进行配置以启用 BitLocker、启用防病毒软件以及启用防火墙|
|**要求实现用户移动设备管理**|要求用来访问电子邮件的用户设备由 Intune 进行管理或者只能通过受 Intune 应用保护策略保护的移动电子邮件应用（例如 Outlook Mobile）访问公司电子邮件|
|**在托管设备上应用 Intune 设备符合性策略**|对托管的公司移动设备和 Intune 管理的 PC 应用 Intune 设备符合性策略，该策略要求具有：长度最少为 6 的 PIN、设备加密、正常运行的设备（未越狱、未 root；通过了运行状况证明）；并且，如果可行，还要求设备经第三方 MTP（例如 Lookout 或 SkyCure）确定为低风险的。|

### <a name="user-impact"></a>用户影响
对于大多数组织，能够针对期望用户在何时以及在什么情况下将登录到 Office 365 电子邮件而设定用户期望非常重要。 

* 单一登录会话的最长生存期为 1 天。 在会话失效后将要求用户通过 MFA 重新进行身份验证。
* 当处于风险中的用户登录时，在完成 MFA 后，会要求他们更改其密码。
* 请求 Exchange Online 的身份验证令牌时：
  * 每当用户开始新会话时都会要求其执行 MFA。  
  * 将要求用户使用支持 Intune 应用保护 SDK 的电子邮件应用
  * 将要求用户从符合 Intune 的或已加入 AD 域的设备访问电子邮件。 
 
 ## <a name="next-steps"></a>后续步骤
 [部署建议的安全电子邮件策略](secure-email-deploy-recommended-policies.md)

