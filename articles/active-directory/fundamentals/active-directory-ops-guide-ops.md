---
title: Azure 活动目录常规操作指南参考
description: 此操作参考指南描述为确保常规操作而应采取的检查和操作
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
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422949"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure 活动目录常规操作指南参考

Azure AD[操作参考指南](active-directory-ops-guide-intro.md)的这一部分介绍了为优化 Azure 活动目录 （Azure AD） 的一般操作而应执行的检查和操作。

> [!NOTE]
> 这些建议自发布之日起是最新的，但可能会随时间而变化。 随着 Microsoft 产品和服务随时间推移而变化，组织应持续评估其运营实践。

## <a name="key-operational-processes"></a>关键操作流程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配给关键任务

管理 Azure 活动目录需要持续执行关键操作任务和进程，这些任务和进程可能不是推出项目的一部分。 设置这些任务以优化环境仍然很重要。 关键任务及其推荐的所有者包括：

| 任务 | “所有者” |
| :- | :- |
| 推动对身份安全分数的改进 | InfoSec 运营团队 |
| 维护 Azure AD 连接服务器 | IAM 运营团队 |
| 定期执行和会审 IdFix 报告 | IAM 运营团队 |
| 分诊 Azure AD 连接同步和 AD FS 的运行状况警报 | IAM 运营团队 |
| 如果不使用 Azure AD 连接运行状况，则客户具有等效的过程和工具来监视自定义基础结构 | IAM 运营团队 |
| 如果不使用 AD FS，则客户具有监视自定义基础结构的等效流程和工具 | IAM 运营团队 |
| 监视混合日志：Azure AD 应用代理连接器 | IAM 运营团队 |
| 监视混合日志：传递身份验证代理 | IAM 运营团队 |
| 监视混合日志：密码回写服务 | IAM 运营团队 |
| 监控混合日志：本地密码保护网关 | IAM 运营团队 |
| 监视混合日志：Azure MFA NPS 扩展（如果适用） | IAM 运营团队 |

在查看列表时，您可能会发现需要为缺少所有者的任务分配所有者，或者调整所有者与上述建议不一致的任务的所有权。

#### <a name="owners-recommended-reading"></a>业主推荐阅读

- [在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的监管](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>混合管理

### <a name="recent-versions-of-on-premises-components"></a>本地组件的最新版本

拥有最新版本的本地组件可为客户提供所有最新的安全更新、性能改进以及有助于进一步简化环境的功能。 大多数组件都有自动升级设置，这将自动执行升级过程。

这些组件包括：

- Azure AD Connect
- Azure AD 应用程序代理连接器
- Azure AD 直通身份验证代理
- Azure AD 连接运行状况代理

除非已经建立，否则应定义升级这些组件的过程，并尽可能依赖自动升级功能。 如果发现组件落后六个月或更长时间，应尽快升级。

#### <a name="hybrid-management-recommended-reading"></a>混合管理推荐阅读

- [Azure AD Connect：自动升级](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [了解 Azure AD 应用程序代理连接器 |自动更新](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD 连接运行状况警报基线

组织应部署[Azure AD 连接运行状况](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health)，以监视和报告 Azure AD 连接和 AD FS。 Azure AD 连接和 AD FS 是可能中断生命周期管理和身份验证并导致中断的关键组件。 Azure AD 连接运行状况可帮助监视和获取对本地标识基础结构的见解，从而确保环境的可靠性。

![Azure AD 连接希思架构](./media/active-directory-ops-guide/active-directory-ops-img16.png)

在监视环境运行状况时，必须立即解决任何高严重性警报，然后是较低的严重性警报。

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD 连接运行状况建议读取

- [Azure AD Connect Health 代理安装](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>本地代理日志

某些标识和访问管理服务需要本地代理才能启用混合方案。 示例包括密码重置、直通身份验证 （PTA）、Azure AD 应用程序代理和 Azure MFA NPS 扩展。 操作团队必须使用系统中心操作管理器或 SIEM 等解决方案存档和分析组件代理日志，从而对这些组件的运行状况进行基线和监视。 同样重要的是，您的 Infosec 运营团队或帮助台了解如何排除错误模式的故障。

#### <a name="on-premises-agents-logs-recommended-reading"></a>本地代理记录建议读取

- [排查应用程序代理问题](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [自助密码重置疑难解答 - Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [了解 Azure AD 应用程序代理连接器](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD 连接：故障排除直通身份验证](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [对 Azure MFA NPS 扩展的错误代码进行故障排除](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>本地代理管理

采用最佳实践有助于本地代理的最佳操作。 请考虑以下最佳实践：

- 建议每个连接器组使用多个 Azure AD 应用程序代理连接器，通过避免访问代理应用程序时的单点故障来提供无缝的负载平衡和高可用性。 如果连接器组中目前只有一个连接器来处理生产中的应用程序，则应至少部署两个连接器以进行冗余。
- 为调试目的创建和使用应用代理连接器组可用于故障排除方案和在加入新的本地应用程序时。 我们还建议在连接器计算机中安装网络工具，如消息分析器和 Fiddler。
- 建议使用多个直通身份验证代理，通过避免身份验证流期间的单点故障来提供无缝的负载平衡和高可用性。 请务必至少部署两个传递身份验证代理以进行冗余。

#### <a name="on-premises-agents-management-recommended-reading"></a>本地代理管理建议读取

- [了解 Azure AD 应用程序代理连接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Azure AD 直通身份验证 - 快速入门](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>规模管理

### <a name="identity-secure-score"></a>标识安全分数

[标识安全分数](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)提供了组织安全状态的可量化度量。 不断审查和处理报告的调查结果，并努力获得尽可能的最高分数是关键。 评分有助于：

- 客观衡量标识安全状况
- 规划标识安全改进
- 审查改进措施的成败

![安全功能分数](./media/active-directory-ops-guide/active-directory-ops-img17.png)

如果您的组织当前没有计划来监视身份安全分数中的更改，建议您实施计划并分配所有者来监视和推动改进操作。 组织应尽快修复分数影响高于 30 的改进操作。

### <a name="notifications"></a>通知

Microsoft 向管理员发送电子邮件通信，以通知服务中的各种更改、所需的配置更新以及需要管理员干预的错误。 客户必须设置通知电子邮件地址，以便通知发送给能够确认并对所有通知执行操作的适当团队成员。 我们建议您将多个收件人添加到[Office 365 邮件中心](https://docs.microsoft.com/office365/admin/manage/message-center)，并要求将通知（包括 Azure AD 连接运行状况通知）发送到通讯组列表或共享邮箱。 如果您只有一个具有电子邮件地址的全局管理员帐户，请确保配置至少两个支持电子邮件的帐户。

Azure AD 使用两个"From"地址：，<o365mc@email2.microsoft.com>它发送 Office 365 消息中心通知;和<azure-noreply@microsoft.com>， 发送与：

- [Azure AD 访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD 标识保护](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD 特权标识管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [企业应用到期证书通知](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- 企业应用预配服务通知

请参阅下表以了解发送的通知类型以及检查位置：

| 通知源 | 发送的内容 | 在哪里检查 |
|:-|:-|:-|
| 技术联系人 | 同步错误 | Azure 门户 - 属性边栏选项卡 |
| Office 365 消息中心 | 身份服务和 O365 后端服务的事故和降级通知 | Office 门户 |
| 身份保护每周摘要 | 身份保护文摘 | Azure AD 标识保护边栏选项卡 |
| Azure AD Connect Health | 警报通知 | Azure 门户 - Azure AD 连接运行状况边栏选项卡 |
| 企业应用程序通知 | 证书即将过期和预配错误的通知 | Azure 门户 - 企业应用程序边栏选项卡（每个应用都有自己的电子邮件地址设置） |

#### <a name="notifications-recommended-reading"></a>通知建议阅读

- [更改组织的地址、技术联系人等 - Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>操作表面积

### <a name="ad-fs-lockdown"></a>AD FS 锁定

组织，它们配置应用程序以直接从 Azure AD 进行身份验证，这些好处来自 [Azure AD 智能锁定](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)。 如果在 Windows 服务器 2012 R2 中使用 AD FS，则实施 AD FS [外网锁定保护](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)。 如果您在 Windows 服务器 2016 或更高版本中使用 AD FS，则实施 [外联网智能锁定](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)。 至少，我们建议您启用外网锁定，以包含对本地活动目录进行暴力攻击的风险。 但是，如果您在 Windows 2016 或更高版本中具有 AD FS，则还应启用外联网智能锁定，这将有助于缓解[密码喷射](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)攻击。

如果 AD FS 仅用于 Azure AD 联合，则可以关闭一些终结点以最小化攻击表面积。 例如，如果 AD FS 仅用于 Azure AD，则应禁用 WS-Trust 终结点，而不是为**用户名混合**和**窗口传输**启用的终结点。

### <a name="access-to-machines-with-on-premises-identity-components"></a>访问具有本地标识组件的计算机

组织应锁定对具有本地混合组件的计算机的访问，其方式与本地域相同。 例如，备份操作员或 Hyper-V 管理员不应能够登录到 Azure AD 连接服务器以更改规则。

Active Directory 管理层模型旨在使用一组缓冲区保护标识系统，这些缓冲区介于对环境的完全控制 （第 0 层）和攻击者经常危害的高风险工作站资产之间。 ![层模型的三个层图示](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[层模型](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)由三个级别组成，仅包括管理帐户，不包括标准用户帐户。

- **第 0** 层 - 直接控制环境中的企业标识。 第 0 层包括帐户、组以及对 Active Directory 林、域或域控制器及其中的所有资产具有直接或间接管理控制权的其他资产。 第 0 层中所有资产的安全敏感性是等同的，因为它们均可以有效地相互控制。
- **第 1** 层 - 控制企业服务器和应用程序。 第 1 层资产包括服务器操作系统、云服务和企业应用程序。 第 1 层的管理员帐户能够管理控制这些资产上托管的大量业务价值。 常见的示例角色是服务器管理员，此角色可维护这些操作系统并能够影响所有企业服务。
- **第 2** 层 - 控制用户工作站和设备。 第 2 层的管理员帐户对用户工作站和设备上托管的大量业务价值具有管理控制权。 示例包括技术支持和计算机支持管理员，因为它们可以影响几乎任何用户数据的完整性。

锁定对本地标识组件（如 Azure AD 连接、AD FS 和 SQL 服务）的访问，与域控制器的访问相同。

## <a name="summary"></a>总结

安全标识基础结构有七个方面。 此列表将帮助您找到为优化 Azure 活动目录 （Azure AD） 的操作而应执行的操作。

- 将所有者分配给关键任务。
- 自动执行本地混合组件的升级过程。
- 部署 Azure AD 连接运行状况，用于监视和报告 Azure AD 连接和 AD FS。
- 使用系统中心操作管理器或 SIEM 解决方案存档和分析组件代理日志，从而监视本地混合组件的运行状况。
- 使用身份安全分数测量安全状态，实现安全改进。
- 锁定 AD FS。
- 锁定对具有本地标识组件的计算机的访问。

## <a name="next-steps"></a>后续步骤

有关尚未部署的任何功能的实现详细信息，请参阅[Azure AD 部署计划](active-directory-deployment-plans.md)。
