---
title: Azure Active Directory PoC 演练手册的实现 | Microsoft Docs
description: 研究并快速实现标识和访问管理方案
services: active-directory
keywords: azure active directory 操作手册, 概念证明, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 2f01bdee5ffcc9e2f48278c7209da40ef64cc030
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068848"
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Azure Active Directory 概念证明演练手册：实现

## <a name="foundation---syncing-ad-to-azure-ad"></a>基础 - 将 AD 同步到 Azure AD 

混合标识是已部署本地目录的大部分企业客户的基础。 混合标识的目标是有意地尽量花费最少的时间来展示实际标识和访问方案的价值。 

| 场景 | 构建基块| 
| --- | --- |  
| [将本地标识扩展到云](#extending-your-on-premises-identity-to-the-cloud) | [目录同步 - 密码哈希同步](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**注意**：如果已安装 DirSync/ADSync 或旧版的 Azure AD Connect，则此步骤是可选的。 本指南中的某些方案可能需要更新版本的 Azure AD Connect。  <br/>[品牌](active-directory-playbook-building-blocks.md#branding) | 
| [使用组分配 Azure AD 许可证](#assigning-azure-ad-licenses-using-groups) | [基于组的许可](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>将本地标识扩展到云 

1. Bob 是 Contoso 公司的 Active Directory 管理员。 公司要求他为一组用户启用“标识即服务”。 执行 Azure AD Connect 向导后，云中已提供目标用户的标识。 
2. Bob 请求 Susie（一个目标用户）访问 Azure Active Directory 访问面板并确认她是否可以进行身份验证。 Susie 看到了带品牌的登录页以及一个空白的访问面板，以后随后可以使用该面板实现应用程序访问。

### <a name="assigning-azure-ad-licenses-using-groups"></a>使用组分配 Azure AD 许可证 

1. Bob 是 Azure AD 全局管理员，他希望在首次推出 Azure AD 的过程中，将 Azure AD 许可证分配给一组特定的用户。
2. Bob 为试点用户创建一个组。 
3. Bob 将许可证分配到该组
4. Susie（一名信息工作者）已被添加到安全组（属于她任职的部门）
5. 经过一段时间后，Susie 可以访问 Azure AD Premium 许可证。 以后，可以使用该许可证实现更多的 POC 方案。

## <a name="theme---lots-of-apps-one-identity"></a>主题 - 多个应用，一个标识

| 场景 | 构建基块| 
| --- | --- |  
| [集成 SaaS 应用程序 - 联合 SSO](#integrate-saas-applications---federated-sso) | [SaaS 联合 SSO 配置](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[组 - 委派的所有权](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [集成 SaaS 应用程序 - 密码 SSO](#integrate-saas-applications---password-sso) | [SaaS 密码 SSO 配置](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [SSO 和标识生命周期事件](#sso-and-identity-lifecycle-events) | [SaaS 和标识生命周期](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [对共享帐户进行安全访问](#secure-access-to-shared-accounts) | [SaaS 共享帐户配置](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [对本地应用程序的安全远程访问](#secure-remote-access-to-on-premises-applications) | [应用代理配置](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [将 LDAP 标识同步到 Azure AD](#synchronize-ldap-identities-to-azure-ad) |  [通用 LDAP 连接器配置](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>集成 SaaS 应用程序 - 联合 SSO 

1. Bob 是 Azure AD 全局管理员，营销部请求他启用对该部门的 ServiceNow 实例的访问。 Bob 在 Azure AD 文档中找到了分步教程，并遵循该教程委派 Kevin（营销团队的领导）将用户分配到应用。 
2. Kevin 以 ServiceNow 资产所有者的身份登录，然后将 Susie 分配到了应用。 Kevin 还告知已在 ServiceNow 中自动创建 Susie 的个人资料
3. Susie 是营销部的信息工作者。 她登录到 Azure AD，并在 myapps 门户中找到了她所分配到的所有 SaaS 应用程序。 在该门户中，她可以顺利地访问 ServiceNow。
4. 营销部想要审核谁访问了 ServiceNow。 Bob 下载活动报告，并通过电子邮件与 Kevin 共享该报告。  

### <a name="sso-and-identity-lifecycle-events"></a>SSO 和标识生命周期事件

1. Susie 请了假，根据公司策略，她的本地 AD 帐户会被暂时禁用。 Susie 现在无法登录到 Azure AD，因此无法访问 ServiceNow。 
2. Susie 从营销部平级调动了到销售部。 Kevin 从 ServiceNow 中删除了其访问权限。 Susie 登录到 Azure AD myapps，但再也看不到 ServiceNow 磁贴。 10 分钟后，Kevin 确认已通过 ServiceNow 管理控制台禁用 Susie 的帐户。

### <a name="integrate-saas-applications---password-sso"></a>集成 SaaS 应用程序 - 密码 SSO

1. Bob 配置了对 Atlassian HipChat 的访问。 HipChat 采用密码 SSO 集成，向 Susie 授予了访问权限
2. Susie 登录到 myapps 门户，看到一个用于下载 Azure AD IE 浏览器扩展的链接，她想要下载该扩展
3. 单击链接时，系统提示她提供 HipChat 用户名和密码凭据。 这是一次性的操作，填写这些凭据后，她便可以访问 HipChat
4. 几天后，Susie 打开 myapps 门户并再次单击 HipChat。 这一次，她可以顺畅地进行访问
5. Kevin（HipChat 应用所有者）想要审核谁访问了应用程序。 Bob 下载审核报告，并通过电子邮件与 Kevin 共享该报告。 

### <a name="secure-access-to-shared-accounts"></a>对共享帐户进行安全访问 

1. Bob 的任务保护销售团队成员的共享 Twitter 帐户。 他将 Twitter 添加为 SSO 应用程序，并将它分配给销售团队的安全组。 公司为他提供了该共享帐户的凭据，他在系统中提供了这些凭据。 
2. 共享 Twitter 凭据的做法不再安全，因为会有很多人知道此信息。 Bob 启用 Twitter 密码自动滚动更新。
3. Susie（销售团队的成员）登录到 myapps 门户，看到一个用于下载 Azure AD IE 浏览器扩展的链接。 她想要安装该扩展。
4. 单击链接时，她直接转到了 Twitter。 她不知道密码。
5. Arnold 也是销售团队的成员。 他遇到了步骤 3-4 中 Susie 所遇到的相同问题
6. 销售部想要审核谁访问了 Twitter。 Bob 下载活动报告，并通过电子邮件与 Kevin 共享该报告。 

### <a name="secure-remote-access-to-on-premises-applications"></a>对本地应用程序的安全远程访问

1. Bob（Azure AD 全局管理员）收到了大量请求，请他想办法让远程办公的员工访问一些有用的本地资源，例如开支应用程序。 他根据[应用程序代理文档](manage-apps/application-proxy-enable.md)安装了连接器，并将 Expenses 发布为应用程序代理应用程序。 
2. Bob 向 Susie（一名需要远程访问权限的员工）共享了外部 Expenses 应用程序的 URL。 她访问该链接，针对 AAD 进行身份验证后，可以访问 Expenses 应用程序并继续在远程高效地工作。 
3. 然后，Bob 继续使用相同的过程发布其他本地应用程序，并根据需要向用户提供访问权限。 对于发布的较敏感应用程序，他添加了条件访问和多重身份验证，确保提高安全性。

### <a name="synchronize-ldap-identities-to-azure-ad"></a>将 LDAP 标识同步到 Azure AD

1. Bob 所在的公司采用复杂的标识基础结构。 大多数用户在 Windows Server Active Directory 域服务 (ADDS) 内部进行维护。 其中一些用户由 Active Directory 轻型目录服务 (ADLDS) 中的 HR 系统管理。
2. Bob 的任务是为所有用户（包括 ADDS 中不存在的用户）启用对 SaaS 应用的访问。
3. Bob 配置通用 LDAP 连接器用于从 Azure AD Connect 中的 ADLDS 提取数据。
4. Bob 创建同步规则，以便将 LDAP 用户填充到 Metaverse 和 Azure AD 中
5. Susie（LDAP 用户）使用同步的标识访问其 SaaS 应用



> [!IMPORTANT] 
> 这是一项高级配置，需要对 FIM/MIM 有一定的了解。 如果在生产环境中使用，我们建议浏览[顶级支持](https://support.microsoft.com/premier)了解有关此配置的问题。



## <a name="theme---increase-your-security"></a>主题 - 增强安全性 

| 场景 | 构建基块| 
| --- | --- |  
| [确保管理员帐户访问安全](#secure-administrator-account-access) | [使用电话呼叫执行 Azure MFA](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [保护对应用程序的访问](#secure-access-to-applications) | [SaaS 应用程序的条件访问](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [启用适时管理](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [根据风险保护标识](#protect-identities-based-on-risk) | [发现风险事件](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[部署登录风险策略](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [使用基于证书的身份验证，在没有密码的情况下进行身份验证](#authenticate-without-passwords-using-certificate-based-authentication) | [配置基于证书的身份验证](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>保护对管理员帐户的访问

1. Bob 是 Azure AD 全局管理员。 他将 Stuart 指定为服务的共同管理员。 
2. 为了提高安全性，Bob 将 Stuart 的帐户配置为始终需要执行 MFA
3. 登录到 Azure 门户时，Stuart 发现需要注册电话号码才能继续登录
4. Stuart 的后续登录受到多重身份验证的保护，现在他需要拨打电话来验证自己的身份。

### <a name="secure-access-to-applications"></a>保护对应用程序的访问

1. Kevin 是 ServiceNow 的业务所有者。 现在，公司希望用户从公司网络外部访问时，必须使用 MFA 登录。
2. Bob（Azure AD 全局管理员）将条件访问策略添加到 ServiceNow 应用程序，针对外部访问启用 MFA
3. Susie（信息工作者）登录到 myapps 门户，并单击 ServiceNow 磁贴。 现在，她在 MFA 上遇到一个难题。

### <a name="enable-just-in-time-jit-administration"></a>启用适时 (JIT) 管理

1. Bob 和 Stuart 都是 Azure AD 全局管理员。 他们想要启用对管理角色的 JIT 访问，同时保留特权角色的使用记录。
2. Bob 在 Azure AD 租户中启用 PIM，并成为安全管理员。 他将自己和 Stuart 的全局管理员角色成员身份从“永久”更改为“符合条件”。
3. 现在，在对 Azure AD 配置进行任何更改之前，Bob 和 Stuart 需要通过 Azure 门户激活其角色。 

### <a name="protect-identities-based-on-risk"></a>根据风险保护标识 

1. Susie（信息工作者）尝试从 Tor 浏览器登录。 
2. Bob 查看 Azure AD Identity Protection 仪表板，发现 Susie 是使用匿名 IP 地址登录的。 安全团队希望对此类 MFA 用户访问活动进行质询
3. Bob 启用 Azure AD Identity Protection 策略，针对中高风险事件质询 MFA
4. 一段时间后，Susie 又可以从 Tor 浏览器登录。 这一次，她会看到 MFA 质询

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>使用基于证书的身份验证，在没有密码的情况下进行身份验证

1. Bob 是某家金融机构的全局管理员，该机构禁止使用密码作为应用程序的身份验证因素。
2. Bob 在 ADFS 和 Azure AD 中启用并实施证书身份验证
3. Susie 访问应用程序时，系统提示她使用证书进行身份验证

## <a name="theme---scale-with-self-service"></a>主题 - 使用自助服务进行缩放

| 场景 | 构建基块| 
| --- | --- |  
| [自助密码重置](#self-service-password-reset) | [自助密码重置](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [对应用程序进行自助访问](#self-service-access-to-applications) | [对应用程序进行自助访问](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>自助密码重置 

1. Bob 是 Azure AD 全局管理员，他针对一部分用户（包括 Susie）启用了自助密码管理。 
2. 登录到 myapps 门户时，Susie 看到一条消息，要求她注册安全信息，以便将来发生密码重置事件时使用。
3. 几天后，Susie 忘记了密码，于是通过 Azure AD 门户重置了密码

### <a name="self-service-access-to-applications"></a>对应用程序进行自助访问 

1. Kevin 是 ServiceNow 的业务所有者。 他希望用户根据需要“注册”该应用程序，而不是一次性为所有用户添加该应用程序的访问权限
2. Bob（Azure AD 全局管理员）修改 ServiceNow 应用程序，启用了自助服务请求
3. Susie（信息工作者）登录到 myapps 门户并单击“添加更多应用程序”按钮，随后看到 ServiceNow 已列为建议的应用程序之一。 导航回到 myapps 门户后，她看到了 ServiceNow 应用程序。

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]