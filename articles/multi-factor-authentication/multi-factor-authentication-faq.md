---
title: "Azure 多重身份验证常见问题解答 | Microsoft Docs"
description: "与 Azure 多重身份验证相关的常见问题与解答。 Azure 多重身份验证是要求使用多种方式（而不仅仅是用户名和密码）来验证用户身份的一种方法。 它为用户登录和事务提供了额外的安全层。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 12b67d92fb955d9d20b0e86a38baa47a3717a333
ms.lasthandoff: 03/31/2017

---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>有关 Azure 多重身份验证的常见问题解答
本“常见问题解答”文章解答有关 Azure 多重身份验证和使用多重身份验证服务的常见问题。 其中的问题已划分为常规服务问题、计费模式问题、用户体验问题和故障排除问题。 

## <a name="general"></a>常规
**问：Azure 多重身份验证服务器如何处理用户数据？**

使用多重身份验证服务器时，用户数据只存储在本地服务器中。 云中不会持久存储任何用户数据。 当用户执行双重验证时，多重身份验证服务器将数据发送到 Azure 多重身份验证云服务执行身份验证。 多重身份验证服务器与多重身份验证云服务之间的通信在出站端口 443 上使用安全套接字层 (SSL) 或传输层安全性 (TLS)。

当身份验证请求发送到云服务时，收集的数据用于身份验证和使用情况报告。 双重验证日志中包含的数据字段如下：

* **唯一 ID**（用户名或本地多重身份验证服务器 ID）
* **姓名**（可选）
* **电子邮件地址**（可选）
* **电话号码**（用于语音通话或短信身份验证）
* **设备令牌**（用于移动应用身份验证）
* **身份验证模式**
* **身份验证结果**
* **多重身份验证服务器名称**
* **多重身份验证服务器 IP**
* **客户端 IP**（如果可用）

可以在多重身份验证服务器中配置可选字段。

验证结果（成功或拒绝）和任何拒绝原因（如果有）与身份验证数据一起存储。 可在身份验证和使用情况报告中使用这些数据。

## <a name="billing"></a>计费
可以参考多[重身份验证定价页](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)或者有关[如何获取 Azure 多重身份验证](multi-factor-authentication-versions-plans.md)的文档解答大多数计费问题。

**问：通过电话或短信进行身份验证时，我的组织是否需要付费？**

否。对于通过 Azure 多重身份验证拨打的每个电话或者向用户发送的每条短信，你都不需要付费。 如果使用预身份验证 MFA 提供程序，则需要为每次身份验付费，但不需要为使用的方法付费。

你的用户可能需要为收到的电话或短信支付费用，取决于个人电话服务。

**问：按用户计费模式的收费依据是所有启用的用户，还是执行双重验证的用户？**

计费依据是配置为使用多重身份验证的用户数目，不管他们是当月是否执行过验证。 

**问：多重身份验证如何计费？**

如果你以独立服务的形式（创建按用户或按身份验证的 MFA 提供程序）购买 Azure 多重身份验证，我们将根据用量按月向你的组织收取 Azure 订阅费。 此计费模式类似于 Azure 收取虚拟机和网站的使用费。

如果你购买 Azure 多重身份验证的订阅（以按用户的年度许可证形式，或者通过 Office 365、Azure AD Premium 或企业移动性 + 安全性捆绑包），则你的组织只需支付每个用户的年度许可证费。

在[如何获取 Azure 多重身份验证](multi-factor-authentication-versions-plans.md)中了解各选项的详细信息。

**问：Azure 多重身份验证是否有免费版本？**

在某些情况下，是。 

面向 Azure 管理员的多重身份验证免费提供一部分 Azure MFA 功能让管理员访问 Microsoft 在线服务，包括 Azure 和 Office 365 管理门户。 这项优惠适用于未通过 MFA 许可证、捆绑包或基于使用量的独立提供程序获得 Azure MFA 完整版本的 Azure Active Directory 实例中的 Azure 管理员。 如果管理员使用免费版本，而你创建了多重身份验证提供程序，则目录中已配置为使用多重身份验证的所有管理员和用户将获得完整版本的 Azure 多重身份验证。

面向 Office 365 用户的多重身份验证免费提供一部分 Azure MFA 功能让用户访问 Microsoft 在线服务，包括 Exchange Online、SharePoint Online 和其他 Office 365 服务。 如果 Azure Active Directory 的相应实例未通过 MFA 许可证、捆绑包或基于使用量的独立提供程序获得 Azure MFA 完整版本，这项优惠将适用于已获得 Office 365 许可证的用户。 

**问：组织是否可以随时在“按用户”和“按身份验证”使用量计费模式之间切换？**

如果你的组织以独立服务的形式（采用基于使用量的计费模式）购买了 MFA，则你可以在创建 MFA 提供程序时选择计费模式。 创建 MFA 提供程序后，无法更改计费模式。 但是，可以删除该 MFA 提供程序，然后创建一个采用不同计费模式的新提供程序。 

创建 MFA 提供程序时，可将它链接到 Azure Active Directory（也称为“Azure AD 租户”）。 如果当前 MFA 提供程序已链接到 Azure AD 租户，则你可以安全删除该 MFA 提供程序，然后创建与同一 Azure AD 租户链接的新 MFA 提供程序。 或者，如果你购买了足够多的 MFA、Azure AD Premium 或企业移动性 + 安全性 (EMS) 许可证，可以覆盖启用 MFA 的所有用户，则可以删除整个 MFA 提供程序。

但是，如果 MFA 提供程序未链接到 Azure AD 租户，或者你将新的 MFA 提供程序链接到了其他 Azure AD 租户，则用户设置和配置选项不会传输到新的 MFA 提供程序。 此外，需要使用通过新 MFA 提供程序生成的激活凭据重新激活现有的 Azure MFA 服务器。 重新激活 MFA 服务器并将其链接到新 MFA 提供程序不会影响电话和短信身份验证，但所有用户不再会收到移动应用通知，除非他们重新激活移动应用。

在 [Azure 多重身份验证提供程序入门](multi-factor-authentication-get-started-auth-provider.md)中了解有关 MFA 提供程序的详细信息。

**问：组织是否可以随时在使用量计费和订阅（基于许可证的模式）之间切换？**

在某些情况下，是。 

如果目录中包含*按用户* Azure 多重身份验证提供程序，则你可以添加 MFA 许可证。 拥有许可证的用户不会计入基于使用量的按用户计费。 对于没有许可证的用户，仍可通过 MFA 提供程序启用 MFA。 如果为配置为使用多重身份验证的所有用户购买并分配许可证，可以删除 Azure 多重身份验证提供程序。 如果将来的用户数超过许可证数，你始终可以创建另一个按用户的 MFA 提供程序。 

如果目录中包含*按身份验证* Azure 多重身份验证提供程序，则你始终要为每次身份验证付费，前提是 MFA 提供程序已链接到你的订阅。 可以将 MFA 许可证分配给用户，但仍要为每个双重验证请求付费，不管该请求是否来自拥有 MFA 许可证的用户。 

**问：组织是否必须使用并同步标识才能使用 Azure 多重身份验证？**

如果组织使用基于使用量的计费模式，则 Azure Active Directory 是可选而不是必需的。 如果 MFA 提供程序未链接到 Azure AD 租户，你只能在本地部署 Azure 多重身份验证服务器或 Azure 多重身份验证 SDK。

许可模式需要 Azure Active Directory，因为在购买许可证并将其分配给目录中的用户时，许可证将添加到 Azure AD 租户。

## <a name="manage-and-support-user-accounts"></a>管理和支持用户帐户

**问：如果用户的手机未收到响应，或者用户没带手机，我该怎么告诉他们？**

但愿你的用户已配置多种验证方法。 请告诉他们再次尝试登录，但需要在登录页上选择另一种验证方法。 

可以让用户转到[最终用户故障排除指南](./end-user/multi-factor-authentication-end-user-troubleshoot.md)。


**问：如果某个用户无法进入其帐户，我该办什么？**

可以要求用户再次完成注册过程来重置其帐户。 详细了解[管理云中 Azure 多重身份验证的用户和设备设置](multi-factor-authentication-manage-users-and-devices.md)。

**问：如果某个用户丢失了使用应用密码的手机，我该办什么？**

为了防止未经授权的访问，请删除该用户的所有应用密码。 用户购买替代设备后，即可重新创建密码。 详细了解[管理云中 Azure 多重身份验证的用户和设备设置](multi-factor-authentication-manage-users-and-devices.md)。

**问：如果用户无法登录到非浏览器应用，该怎么办？**

如果组织仍然使用传统客户端，并且[允许使用应用密码](multi-factor-authentication-whats-next.md#app-passwords)，则用户无法使用其用户名和密码登录到这些传统客户端。 他们需要[设置应用密码](./end-user/multi-factor-authentication-end-user-app-passwords.md)。 用户必须清除（删除）其登录信息、重新启动应用，然后使用其用户名和*应用密码*而不是普通的密码登录。 

如果组织不使用传统客户端，则你不应该允许用户创建应用密码。 

> [!NOTE]
> 适用于 Office 2013 客户端的新式验证
> 
> 只有不支持新式身份验证的应用才需要应用密码。 Office 2013 客户端支持新式身份验证协议，但需要进行配置。 新式 Office 客户端自动支持新式身份验证协议。 有关详细信息，请参阅 [Office 2013 现代身份验证公共预览版发布声明](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

**问：我的用户指出，有时他们收不到短信，或者回复了双向短信但验证超时。**

发送短信和接收双向短信回复无法得到保障，因为它们属于可能影响服务可靠性的不可控因素。 这些因素包括目标国家/地区、移动电话运营商和信号强度。

如果用户经常无法可靠地接收短信，请告诉他们改用移动应用或电话验证方法。 移动应用可以同时通过手机网络和 Wi-Fi 连接接收通知。 此外，即使设备根本没有信号，也可以生成验证码。 Microsoft 验证器应用适用于 [Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 和 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)。

如果必须使用短信，建议尽可能使用单向短信，而不要使用双向短信。 单向短信更加可靠，可以防止由于回复其他国家/地区发来的短信而给用户造成的全球短信费用。

**问：是否可以更改在系统超时之前，用户必须输入短信中验证代码的时限？**

在某些情况下可以。 可以在 Azure MFA 服务器 7.0 和更高版本中配置双向短信的超时设置。 

默认情况下，Azure MFA 服务器会存储一次性密码 300 秒（5 分钟）。 如果用户在 300 秒后输入其代码，其身份验证将被拒绝。 可以通过设置注册表项来调整超时。 

1. 转到 HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor。
2. 创建名为 **pfsvc_pendingSmsTimeoutSeconds** 的 DWORD 注册表项，并设置希望 Azure MFA 服务器存储一次性密码的时间长短（以秒为单位）。

对于单向短信，MFA 服务器将存储一次性密码 300 秒，而 Azure AD 中基于云的 MFA 将存储一次性密码 180 秒。 此设置不可配置。 

**问：是否可以在 Azure 多重身份验证服务器上使用硬件令牌？**

如果使用的是 Azure 多重身份验证服务器，可以导入第三方基于时间的一次性密码 (TOTP) 开放式身份验证 (OATH) 令牌，然后将其用于双重验证。

如果将机密密钥放在可以导入 Azure 多重身份验证服务器的 CSV 文件中，可以使用充当 OATH TOTP 令牌的 ActiveIdentity 令牌。 可将 OATH 令牌与以下服务配合使用：Active Directory 联合身份验证服务 (ADFS)、、基于 Internet Information Server (IIS) 窗体的身份验证和远程身份验证拨入用户服务 (RADIUS)（如果客户端系统可以处理访问质询响应）。

可以使用以下格式导入第三方 OATH TOTP 令牌：  

- 可移植对称密钥容器 (PSKC)  
- CSV 如果文件包含序列号、Base 32 格式的密钥和时间间隔  

**问：是否可以使用 Azure 多重身份验证服务器保护终端服务？**

可以，但如果使用的是 Windows Server 2012 R2 或更高版本，则只能使用远程桌面网关（RD 网关）保护终端服务。

Windows Server 2012 R2 中的安全性更改改变了 Azure 多重身份验证服务器连接到 Windows Server 2012 和更低版本中的本地安全机构 (LSA) 安全包的方式。 对于 Windows 2012 或更低版本中的终端服务版本，可以[使用 Windows 身份验证保护应用程序](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)。 如果使用的是 Windows Server 2012 R2，需要 RD 网关。

**问：我在 MFA 服务器中配置了来电显示，但用户仍然接到匿名呼叫者的多重身份验证电话。**

通过公共电话网络拨打多重身份验证电话时，有时会通过不支持来电显示的运营商路由电话。 因此，尽管多重身份验证系统始终会发送呼叫号码，但并不保证会显示该号码。

**问：为何系统提示用户注册其安全信息？**
有多种原因会导致系统提示用户注册其安全信息：

- 该用户的管理员已在 Azure AD 中为其启用 MFA，但没有为其帐户注册安全信息。 
- 该用户已在 Azure AD 中启用自助密码重置。 以后如果用户忘记了密码，安全信息可帮助他们重置密码。
- 该用户访问的应用程序配置了一个要求使用 MFA 的条件访问策略，但该应用程序以前未注册 MFA。
- 该用户正在将某个设备注册到 Azure AD（包括 Azure AD Join），并且你的组织要求使用 MFA 进行设备注册，但该用户以前未注册 MFA。
- 该用户正在 Windows 10 中生成 Windows Hello for Business（需要 MFA），但以前未注册 MFA。
- 组织已创建并启用一个 MFA 注册策略，该策略已应用到该用户。
- 该用户以前已注册 MFA，但选择的验证方法后来被管理员禁用。 因此，该用户必须再次完成 MFA 注册，以选择新的默认验证方法。


## <a name="errors"></a>错误
**问：如果用户在使用移动应用通知时看到“身份验证请求不适用于已激活的帐户”错误消息，该怎么办？**

告诉他们按照此过程从移动应用中删除帐户，然后重新添加：

1. 转到 [Azure 门户配置文件](https://account.activedirectory.windowsazure.com/profile/)，然后使用组织帐户登录。
2. 选择“其他安全性验证”。
3. 从移动应用中删除现有帐户。
4. 单击“配置”，然后按照说明重新配置移动应用。

**问：如果用户在登录非浏览器应用程序时看到 0x800434D4L 错误消息，该怎么办？**

尝试登录到安装在本地计算机上的、无法使用需要双重验证的帐户的非浏览器应用程序时，将发生 0x800434D4L 错误。 

此问题的解决方法是使用不同的用户帐户进行管理员相关操作和非管理员操作。 稍后，可以在管理员帐户与非管理员帐户之间链接邮箱，以便能够使用非管理员帐户登录到 Outlook。 有关更多详细信息，请了解如何[使管理员能够打开和查看用户邮箱的内容](http://help.outlook.com/141/gg709759.aspx?sl=1)。

## <a name="next-steps"></a>后续步骤
如果此处未解答你的问题，请在页面底部留言。 或者，通过一些其他方法获得帮助：

* 在 [Microsoft 支持知识库](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)中搜索常见技术问题的解决方法。
* 在 [Azure Active Directory 论坛](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)中搜索和浏览来自社区的技术问题与解答，或者提出自己的问题。
* 对于旧版 PhoneFactor 客户，如果有疑问或需要重置密码方面的帮助，请使用[密码重置](mailto:phonefactorsupport@microsoft.com)链接建立支持案例。
* 通过 [Azure 多重身份验证服务器 (PhoneFactor) 支持](https://support.microsoft.com/oas/default.aspx?prid=14947)联系支持专业人员。 与我们联系时，尽可能包含有关问题的更多信息将很有帮助。 可提供的信息包括看到错误的页面、特定错误代码、特定会话 ID 和看到错误的用户的 ID。


