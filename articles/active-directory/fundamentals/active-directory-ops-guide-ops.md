---
title: Azure Active Directory 常规操作指南参考
description: 此操作参考指南介绍了保护常规操作应采取的检查和操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 46e5af9d54cf818366bd2730de0da85dcbe6cade
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535296"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure Active Directory 常规操作指南参考

本部分的[Azure AD 操作参考指南](active-directory-ops-guide-intro.md)介绍了优化 Azure Active Directory （Azure AD）的常规操作时应采取的检查和操作。

> [!NOTE]
> 这些建议是发布日期之后的最新建议，但会随时间变化。 组织应持续评估其操作做法，因为 Microsoft 产品和服务随时间推移而发展。

## <a name="key-operational-processes"></a>关键操作过程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配到关键任务

管理 Azure Active Directory 需要持续执行关键操作任务和进程，这可能不是一个部署项目的一部分。 设置这些任务以优化环境仍非常重要。 关键任务及其建议所有者包括：

| 任务 | 所有者 |
| :- | :- |
| 提高了对标识安全分数的改进 | InfoSec 运营团队 |
| 维护 Azure AD Connect 服务器 | IAM 操作团队 |
| 定期执行和会审 IdFix 报表 | IAM 操作团队 |
| 会审 Azure AD Connect Health 警报同步和 AD FS | IAM 操作团队 |
| 如果不使用 Azure AD Connect Health，则客户具有用于监视自定义基础结构的等效过程和工具 | IAM 操作团队 |
| 如果不使用 AD FS，则客户具有用于监视自定义基础结构的等效过程和工具 | IAM 操作团队 |
| 监视混合日志： Azure AD 应用代理连接器 | IAM 操作团队 |
| 监视混合日志：直通身份验证代理 | IAM 操作团队 |
| 监视混合日志：密码写回服务 | IAM 操作团队 |
| 监视混合日志：本地密码保护网关 | IAM 操作团队 |
| 监视混合日志： Azure MFA NPS 扩展（如果适用） | IAM 操作团队 |

查看列表时，可能会发现需要为缺少所有者的任务分配所有者，或使用与上述建议不符的所有者来调整任务的所有权。

#### <a name="owners-recommended-reading"></a>所有者建议阅读

- [在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的监管](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>混合管理

### <a name="recent-versions-of-on-premises-components"></a>本地组件的最新版本

具有最新版本的本地组件可为客户提供最新的安全更新、性能改进以及有助于进一步简化环境的功能。 大多数组件都具有自动升级设置，这将自动执行升级过程。

这些组件包括：

- Azure AD Connect
- Azure AD 应用程序代理连接器
- Azure AD 传递身份验证代理
- Azure AD Connect Health 代理

除非已建立一个，否则应该定义一个过程来升级这些组件，并尽可能利用自动升级功能。 如果发现几个月之后出现六个或更多的组件，应该尽快升级。

#### <a name="hybrid-management-recommended-reading"></a>混合管理建议阅读

- [Azure AD Connect：自动升级](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [了解 Azure AD 应用程序代理连接器 |自动更新](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health 警报基线

组织应部署[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health)以便监视和报告 Azure AD Connect 和 AD FS。 Azure AD Connect 和 AD FS 是关键组件，可能会中断生命周期管理和身份验证，从而导致停机。 Azure AD Connect Health 有助于监视和深入了解本地标识基础结构，从而确保环境的可靠性。

![Azure AD Connect Heath 体系结构](./media/active-directory-ops-guide/active-directory-ops-img16.png)

当监视环境的运行状况时，必须立即解决所有高严重性警报，然后再解决严重性较低的警报。

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health 建议阅读

- [Azure AD Connect Health 代理安装](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>本地代理日志

某些标识和访问管理服务要求本地代理启用混合方案。 示例包括密码重置、传递身份验证（PTA）、Azure AD 应用程序代理和 Azure MFA NPS 扩展。 操作团队通过使用 System Center Operations Manager 或 SIEM 等解决方案存档和分析组件代理日志来比较和监视这些组件的运行状况，这一点很重要。 同样重要的是，Infosec 运营团队或咨询台了解如何排查错误模式。

#### <a name="on-premises-agents-logs-recommended-reading"></a>本地代理日志建议阅读

- [排查应用程序代理问题](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [自助密码重置故障排除-Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [了解 Azure AD 应用程序代理连接器](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect：对直通身份验证进行故障排除](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [排查 Azure MFA NPS 扩展的错误代码](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>本地代理管理

采用最佳做法有助于实现本地代理的最佳操作。 请考虑以下最佳做法：

- 建议每个连接器组使用多个 Azure AD 应用程序代理连接器，以便通过避免在访问代理应用程序时出现单一故障点来提供无缝的负载平衡和高可用性。 如果连接器组中目前只有一个连接器用于处理生产中的应用程序，则应至少部署两个连接器以实现冗余。
- 出于调试目的创建和使用应用程序代理连接器组对于排除方案和载入新的本地应用程序很有用。 我们还建议在连接器计算机中安装网络工具，如 Message Analyzer 和 Fiddler。
- 建议使用多个直通身份验证代理来提供无缝的负载平衡和高可用性，方法是避免在身份验证流中出现单点故障。 确保至少部署两个直通身份验证代理以实现冗余。

#### <a name="on-premises-agents-management-recommended-reading"></a>本地代理管理建议阅读

- [了解 Azure AD 应用程序代理连接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Azure AD 传递身份验证-快速入门](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start#step-5-ensure-high-availability)

## <a name="management-at-scale"></a>大规模管理

### <a name="identity-secure-score"></a>标识安全分数

[标识安全分数](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)为组织的安全状况提供了一个可计量度量值。 这是不断查看和解决报告并尽力实现最高分数的关键。 评分有助于：

- 客观衡量标识安全状况
- 规划标识安全改进
- 审查改进措施的成败

![安全功能分数](./media/active-directory-ops-guide/active-directory-ops-img17.png)

如果你的组织当前没有可用于监视身份安全分数变化的计划，则建议你实现计划并分配所有者来监视和推动改进操作。 组织应尽快纠正提高操作的得分，使得分超过30。

### <a name="notifications"></a>通知

Microsoft 向管理员发送电子邮件通信，通知服务中的各种更改、所需的配置更新以及需要管理员干预的错误。 重要的是，客户必须设置通知电子邮件地址，以便将通知发送到正确的团队成员，这些成员可以确认并操作所有通知。 建议将多个收件人添加到[Office 365 消息中心](https://docs.microsoft.com/office365/admin/manage/message-center)，并请求将通知（包括 Azure AD Connect Health 通知）发送到通讯组列表或共享邮箱。 如果只有一个具有电子邮件地址的全局管理员帐户，请确保至少配置两个支持电子邮件的帐户。

Azure AD 提供了两个 "发件人" 地址： <o365mc@email2.microsoft.com>，用于发送 Office 365 消息中心通知;和 <azure-noreply@microsoft.com>，它将发送与以下内容相关的通知：

- [Azure AD 访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD 标识保护](https://docs.microsoft.com/azure/active-directory/identity-protection/notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [企业应用过期证书通知](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- 企业应用预配服务通知

请参阅下表，了解发送的通知类型和要检查的通知：

| 通知源 | 发送内容 | 检查位置 |
|:-|:-|:-|
| 技术联系人 | 同步错误 | Azure 门户-属性边栏选项卡 |
| Office 365 消息中心 | 标识服务和 O365 后端服务的事件和降级通知 | Office 门户 |
| Identity Protection 每周摘要 | Identity Protection 摘要 | Azure AD Identity Protection 边栏选项卡 |
| Azure AD Connect Health | 警报通知 | Azure 门户-Azure AD Connect Health 边栏选项卡 |
| 企业应用程序通知 | 证书即将过期时的通知和预配错误 | Azure 门户-企业应用程序边栏选项卡（每个应用程序都有自己的电子邮件地址设置） |

#### <a name="notifications-recommended-reading"></a>建议阅读通知

- [更改组织的地址、技术联系人和更多-Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>操作外围应用

### <a name="ad-fs-lockdown"></a>AD FS 锁定

组织，它将应用程序配置为直接进行身份验证，以便 Azure AD 受益于 [Azure AD 智能锁定](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)。 如果在 Windows Server 2012 R2 中使用 AD FS，请实现 AD FS [extranet 锁定保护](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)。 如果在 Windows Server 2016 或更高版本上使用 AD FS，请实现 [extranet 智能锁定](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)。 我们至少建议你启用 extranet 锁定，以包含对本地 Active Directory 进行暴力攻击的风险。 但是，如果在 Windows 2016 或更高版本中有 AD FS，还应启用 extranet 智能锁定，以帮助减轻[密码喷涂](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)攻击。

如果 AD FS 仅用于 Azure AD 联合，则可以关闭某些终结点以最大程度地减少受攻击面。 例如，如果 AD FS 仅用于 Azure AD，则应禁用不支持**usernamemixed**和**windowstransport**终结点的 ws-trust 终结点。

### <a name="access-to-machines-with-on-premises-identity-components"></a>使用本地标识组件访问计算机

组织应使用与本地域相同的方式锁定对具有本地混合组件的计算机的访问。 例如，备份操作员或 Hyper-v 管理员应无法登录到 Azure AD Connect 服务器来更改规则。

Active Directory 的管理层模型旨在使用一组缓冲区域来保护标识系统，该环境在完全控制环境（第0层）与攻击者经常泄露的高风险工作站资产之间使用。 ![显示层模型的三个层的关系图](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[层模型](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)由三个级别组成，只包括管理帐户，而不包括标准用户帐户。

- **第0层** -对环境中企业标识的直接控制。 第0层包括帐户、组和其他资产，这些资产对 Active Directory 林、域或域控制器以及其中的所有资产具有直接或间接管理控制。 所有第0层资产的安全敏感度都是等效的，因为它们都可以有效地进行控制。
- **第1层** 控制企业服务器和应用程序。 第1层资源包括服务器操作系统、云服务和企业应用程序。 第1层管理员帐户对这些资产上托管的大量业务价值具有管理控制权限。 常见的示例角色是服务器管理员，它们维护这些操作系统的功能能够影响所有企业服务。
- **第2层** 控制用户工作站和设备。 第2层管理员帐户对用户工作站和设备上托管的大量业务价值具有管理控制。 例如，"技术支持" 和 "计算机支持管理员"，因为它们可能会影响几乎任何用户数据的完整性。

使用与域控制器相同的方式锁定对本地标识组件（例如 Azure AD Connect、AD FS 和 SQL 服务）的访问。

## <a name="summary"></a>摘要

安全标识基础结构有七个方面。 此列表将帮助你找到为 Azure Active Directory （Azure AD）优化操作而应执行的操作。

- 将所有者分配给关键任务。
- 为本地混合组件自动执行升级过程。
- 部署 Azure AD Connect Health 以便监视和报告 Azure AD Connect 和 AD FS。
- 通过使用 System Center Operations Manager 或 SIEM 解决方案存档和分析组件代理日志来监视本地混合组件的运行状况。
- 通过使用标识安全分数衡量安全状况来实现安全改进。
- 锁定 AD FS。
- 使用本地标识组件锁定对计算机的访问。

## <a name="next-steps"></a>后续步骤

有关未部署的任何功能的实现细节，请参阅[Azure AD 部署计划](active-directory-deployment-plans.md)。
