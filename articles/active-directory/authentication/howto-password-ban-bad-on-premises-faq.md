---
title: On-premises password protection FAQ - Azure Active Directory
description: 本地 Azure AD 密码保护常见问题解答
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ee5d6328c6a3e4ea0b4a6359d4a21494e3ae62c
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381702"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>本地 Azure AD 密码保护 - 常见问题解答

This section provides answers to many commonly asked questions about Azure AD Password Protection.

## <a name="general-questions"></a>一般问题

**Q: What guidance should users be given on how to select a secure password?**

在以下链接中可以找到 Microsoft 当前为此主题提供的指导：

[Microsoft 密码指导](https://www.microsoft.com/research/publication/password-guidance)

**Q: Is on-premises Azure AD Password Protection supported in non-public clouds?**

不支持 - 只有公有云才支持本地 Azure AD 密码保护。 对于何时可在非公有云中使用，我们没有具体的日程表。

The Azure AD portal does allow modification of the on-premises-specific "Password protection for Windows Server Active Directory" configuration even in non-public clouds; such changes will be persisted but otherwise will never take effect. Registration of on-premises proxy agents or forests is unsupported when non-public cloud credentials are used, and any such registration attempts will always fail.

**Q: How can I apply Azure AD Password Protection benefits to a subset of my on-premises users?**

不支持。 部署并启用后，Azure AD 密码保护不会区分对待 - 所有用户都会获得均等的安全权益。

**Q: What is the difference between a password change and a password set (or reset)?**

A password change is when a user chooses a new password after proving they have knowledge of the old password. For example, a password change is what happens when a user logs into Windows and is then prompted to choose a new password.

A password set (sometimes called a password reset) is when an administrator replaces the password on an account with a new password, for example by using the Active Directory Users and Computers management tool. This operation requires a high level of privilege (usually Domain Admin), and the person performing the operation usually does not have knowledge of the old password. Help-desk scenarios often perform password sets, for instance when assisting a user who has forgotten their password. You will also see password set events when a brand new user account is being created for the first time with a password.

The password validation policy behaves the same regardless of whether a password change or set is being done. The Azure AD Password Protection DC Agent service does log different events to inform you whether a password change or set operation was done.  See [Azure AD Password Protection monitoring and logging](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Q: Why are duplicated password rejection events logged when attempting to set a weak password using the Active Directory Users and Computers management snap-in?**

The Active Directory Users and Computers management snap-in will first try to set the new password using the Kerberos protocol. Upon failure, the snap-in will make a second attempt to set the password using a legacy (SAM RPC) protocol (the specific protocols used are not important). If the new password is considered weak by Azure AD Password Protection, this snap-in behavior will result in two sets of password reset rejection events being logged.

**Q: Why are Azure AD Password Protection password validation events being logged with an empty user name?**

Active Directory supports the ability to test a password to see if it passes the domain's current password complexity requirements, for example using the [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api. When a password is validated in this way, the testing also includes validation by password-filter-dll based products such as Azure AD Password Protection - but the user names passed to a given password filter dll will be empty. In this scenario, Azure AD Password Protection will still validate the password using the currently in-effect password policy and will issue an event log message to capture the outcome, however the event log message will have empty user name fields.

**Q: Is it supported to install Azure AD Password Protection side by side with other password-filter-based products?**

可以。 支持多个已注册的密码筛选器 dll 是一项 Windows 核心功能，并不特定于 Azure AD 密码保护。 在接受密码之前，所有已注册的密码筛选器 dll 必须同意。

**Q: How can I deploy and configure Azure AD Password Protection in my Active Directory environment without using Azure?**

不支持。 Azure AD 密码保护是旨在扩展到本地 Active Directory 环境中的一项 Azure 功能。

**Q: How can I modify the contents of the policy at the Active Directory level?**

不支持。 The policy can only be administered using the Azure AD portal. 另请参阅前面的问题。

**问：为何需要使用 DFSR 进行 sysvol 复制？**

FRS（DFSR 以前的技术）存在很多已知问题，在较新版本的 Windows Server Active Directory 中完全不受支持。 Azure AD 密码保护的零测试将在配置了 FRS 的域上完成。

有关详细信息，请参阅以下文章：

[将 sysvol 复制迁移到 DFSR 的用例](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[FRS 即将终结](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

If your domain is not already using DFSR, you MUST migrate it to use DFSR before installing Azure AD Password Protection. For more information, see the following link:

[SYSVOL Replication Migration Guide: FRS to DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> The Azure AD Password Protection DC Agent software will currently install on domain controllers in domains that are still using FRS for sysvol replication, but the software will NOT work properly in this environment. Additional negative side-effects include individual files failing to replicate, and sysvol restore procedures appearing to succeed but silently failing to replicate all files. You should migrate your domain to use DFSR as soon as possible, both for DFSR's inherent benefits and also to unblock the deployment of Azure AD Password Protection. Future versions of the software will be automatically disabled when running in a domain that is still using FRS.

**Q: How much disk space does the feature require on the domain sysvol share?**

准确的空间用量根据多种因素而异，例如，Microsoft 全局受禁密码列表和租户自定义密码列表中的受禁令牌数目和长度，以及加密开销。 这些列表的内容将来可能会不断扩充。 考虑到这一点，合理的预期是该功能至少需要占用域 sysvol 共享中的 5 MB 空间。

**问：安装或升级 DC 代理软件后为何需要重新启动？**

此项要求是核心 Windows 行为造成的。

**问：是否有任何方法可将 DC 代理配置为使用特定的代理服务器？**

不。 由于代理服务器是无状态的，具体使用哪种代理服务器并不重要。

**Q: Is it okay to deploy the Azure AD Password Protection Proxy service side by side with other services such as Azure AD Connect?**

可以。 Azure AD 密码保护代理服务和 Azure AD Connect 永远不会直接相互冲突。

Unfortunately, an incompatibility has been found between the version of the Microsoft Azure AD Connect Agent Updater service that is installed by the Azure AD Password Protection Proxy software and the version of the service that is installed by the [Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) software. This incompatibility may result in the Agent Updater service being unable to contact Azure for software updates. It is not recommended to install Azure AD Password Protection Proxy and Azure Active Directory Application Proxy on the same machine.

**Q: In what order should the DC agents and proxies be installed and registered?**

Any ordering of Proxy agent installation, DC agent installation, forest registration, and Proxy registration  is supported.

**Q: Should I be concerned about the performance hit on my domain controllers from deploying this feature?**

在现有的正常 Active Directory 部署中，Azure AD 密码保护 DC 代理服务应该不会显著影响域控制器的性能。

对于大部分 Active Directory 部署而言，密码更改操作在任意给定的域控制器上只占总体工作负荷的一小部分。 例如，假设某个 Active Directory 域包含 10000 个用户帐户，某个 MaxPasswordAge 策略设置为 30 天。 一般情况下，此域每天会发生 10000/30 = 大约 333 次密码更改操作，即使是在单个域控制器中，此操作次数也是微不足道的。 考虑一种更糟糕的潜在情况：假设在单个 DC 上执行的大约 333 次密码更改是在一小时内完成的。 例如，当许多员工在星期一上午上班时，就可能会发生这种此情况。 即使出现这种情况，每 60 分钟的密码更改次数也只有大约 333 次，即每分钟 6 次，同样，这并不是一个很高的负载。

但是，如果当前域控制器已在性能限制级别运行（例如，达到了 CPU、磁盘空间、磁盘 I/O 等的上限），则我们建议添加更多的域控制器或扩展可用磁盘空间，然后再部署此功能。 另请参阅前面有关 sysvol 磁盘空间用量的问题。

**Q: I want to test Azure AD Password Protection on just a few DCs in my domain. Is it possible to force user password changes to use those specific DCs?**

不。 当用户更改其密码时，Windows 客户端 OS 会控制要使用的域控制器。 The domain controller is selected based on factors such as Active Directory site and subnet assignments, environment-specific network configuration, etc. Azure AD Password Protection does not control these factors and cannot influence which domain controller is selected to change a user's password.

在一定程度上实现此目标的方法之一是在给定 Active Directory 站点中的所有域控制器上部署 Azure AD 密码保护。 这种方法能够合理覆盖分配到该站点的 Windows 客户端，因此，也会合理覆盖登录到这些客户端并更改其密码的用户。

**Q: If I install the Azure AD Password Protection DC Agent service on just the Primary Domain Controller (PDC), will all other domain controllers in the domain also be protected?**

不。 如果在给定的非 PDC 域控制器上更改用户密码时，则明文密码永远不会发送到 PDC（这种想法是常见的错误认知）。 一旦在给定的 DC 上接受新密码，该 DC 将使用该密码来为该密码创建各种特定于身份验证协议的哈希，然后在目录中保存这些哈希。 不会保存明文密码。 然后，更新的哈希将复制到 PDC。 在某些情况下，用户密码可以直接在 PDC 上更改，同样，这取决于网络拓扑和 Active Directory 站点设计等多种因素。 （请参阅前面的问题。）

总而言之，在 PDC 上部署 Azure AD 密码保护 DC 代理服务需要对整个跨中的功能实现 100% 的安全覆盖。 仅在 PDC 上部署该功能不能为域中的其他任何 DC 提供 Azure AD 密码保护安全优势。

**Q: Why is custom smart lockout not working even after the agents are installed in my on-premises Active Directory environment?**

Custom smart lockout is only supported in Azure AD. Changes to the custom smart lockout settings in the Azure AD portal have no effect on the on-premises Active Directory environment, even with the agents installed.

**Q: Is a System Center Operations Manager management pack available for Azure AD Password Protection?**

不。

**Q: Why is Azure AD still rejecting weak passwords even though I've configured the policy to be in Audit mode?**

Audit mode is only supported in the on-premises Active Directory environment. Azure AD is implicitly always in "enforce" mode when it evaluates passwords.

**Q: My users see the traditional Windows error message when a password is rejected by Azure AD Password Protection. Is it possible to customize this error message so that users know what really happened?**

不。 The error message seen by users when a password is rejected by a domain controller is controlled by the client machine, not by the domain controller. This behavior happens whether a password is rejected by the default Active Directory password policies or by a password-filter-based solution such as Azure AD Password Protection.

## <a name="additional-content"></a>其他内容

以下链接不是核心 Azure AD 密码保护文档的一部分，但可能有关该功能的其他信息的有用来源。

[Azure AD Password Protection is now generally available!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Email Phishing Protection Guide – Part 15: Implement the Microsoft Azure AD Password Protection Service (for On-Premises too!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)（Azure AD 密码保护和智能锁定现已推出公共预览版！）

## <a name="microsoft-premierunified-support-training-available"></a>可用的 Microsoft Premier\Unified 支持培训

如果你想要详细了解 Azure AD 密码保护并将其部署在自己的环境中，可以利用面向已签署 Premier 或 Unified 合同的客户的 Microsoft 前瞻服务。 The service is called Azure Active Directory: Password Protection. 请联系技术客户经理获取详细信息。

## <a name="next-steps"></a>后续步骤

如果本文未解答你遇到的本地 Azure AD 密码保护问题，请在下方提交反馈 - 谢谢！

[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
