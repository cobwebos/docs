---
title: "混合标识设计 - 采用策略 Azure | Microsoft Docs"
description: "借助条件性访问控制，Azure Active Directory 会在验证用户身份时先检查选取的特定条件，然后才允许访问应用程序。 一旦符合这些条件，用户就会通过身份验证并获权访问应用程序。"
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: e6c9cbc4f158e62092c7a9e401e618880e5ea3b6
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>定义混合标识采用策略
在此任务中，你将根据所述的业务要求，为混合标识解决方案定义混合标识采用策略：

* [确定业务需求](active-directory-hybrid-identity-design-considerations-business-needs.md)
* [确定目录同步要求](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [确定多重身份验证要求](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>定义业务需求策略
第一项任务是确定组织的业务需求。  这可能非常广泛，一不小心就发生范围蔓延。  刚开始要保持简单，但要时常记住所规划的设计应该兼顾和推动将来的变革。  无论是简单还是极为复杂的设计，Azure Active Directory 都是支持 Office 365、Microsoft Online Services 和云感知应用程序的 Microsoft 标识平台。

## <a name="define-an-integration-strategy"></a>定义集成策略
Microsoft 有三个主要集成方案，分别为云标识、同步标识和联合标识。  应该规划采用这些集成策略的其中一个。  选择的策略可能有所不同，决策的因素包括想要提供何种用户体验、是否已部署现有的基础结构，以及哪种做法最符合成本效益。  

![](./media/hybrid-id-design-considerations/integration-scenarios.png)

上图中定义的方案如下：

* **云标识**：这是仅存在于云中的标识。  对于 Azure AD，它们实际上位于 Azure AD 目录中。
* **同步**：这是存在于本地和云中的标识。  使用 Azure AD Connect 时，以现有的 Azure AD 帐户创建或联接这些用户。  在所谓的密码哈希中，用户的密码哈希从本地环境同步到云。  使用“同步”时的一个注意事项是，如果在本地环境中禁用某个用户，可能需要经过三小时，Azure AD 中才显示该帐户状态。  这是因为存在同步时间间隔。
* **联合**：这些标识同时存在于本地和云中。  使用 Azure AD Connect 时，以现有的 Azure AD 帐户创建或联接这些用户。  

> [!NOTE]
> 有关同步选项的详细信息，请阅读 [Integrating your on-premises identities with Azure Active Directory](connect/active-directory-aadconnect.md)（将本地标识与 Azure Active Directory 集成）。
> 
> 

下表可以帮助判断以下每种策略的优缺点：

| 策略 | 优点 | 缺点 |
| --- | --- | --- |
| **云标识** |在小型组织中易于管理。 <br> 不需要在本地安装任何软件 - 不需要额外的硬件<br>用户离职时轻松禁用其帐户 |用户访问云中的工作负荷时需要登录 <br> 云和本地标识的密码可以相同，也可以不同 |
| **同步** |本地密码用于针对本地和云目录进行身份验证 <br>在小型、中型或大型组织中更易于管理 <br>用户可针对某些资源执行单一登录 (SSO) <br> 用于同步的 Microsoft 首选方法 <br> 更易于管理 |由于公司的特定策略，某些客户可能不愿意将目录与云同步 |
| **联合** |用户可以执行单一登录 (SSO) <br>如果用户被辞退或离职，可以立即禁用其帐户并吊销其访问权限。<br> 支持同步所不能实现的高级方案 |设置和配置步骤较多 <br> 更多的维护工作 <br> 可能需要额外的硬件来部署 STS 服务器。 <br> 可能需要额外的硬件来安装联合服务器。 如果使用 AD FS，则需要额外的软件 <br> 需要进行大量的设置才能使用 SSO <br> 严重的缺点是，联合身份验证服务器关闭时用户无法进行身份验证 |

### <a name="client-experience"></a>客户端体验
使用的策略决定用户的登录体验。  下表提供用户预期的登录体验的相关信息。  并非所有联合标识提供者在所有方案中都支持 SSO。

**已加入域的应用程序和专用网络应用程序**：

|  | 同步标识 | 联合标识 |
| --- | --- | --- |
| Web 浏览器 |基于表单的身份验证 |单一登录，有时需要提供组织 ID |
| Outlook |提示输入凭据 |提示输入凭据 |
| Skype for Business (Lync) |提示输入凭据 |在 Lync 中需要单一登录，在 Exchange 中提示输入凭据 |
| Skydrive Pro |提示输入凭据 |单一登录 |
| Office Pro Plus 订阅 |提示输入凭据 |单一登录 |

**外部或不受信任源**：

|  | 同步标识 | 联合标识 |
| --- | --- | --- |
| Web 浏览器 |基于表单的身份验证 |基于表单的身份验证 |
| Outlook、Skype for Business (Lync)、Skydrive Pro、Office 订阅 |提示输入凭据 |提示输入凭据 |
| Exchange ActiveSync |提示输入凭据 |在 Lync 中需要单一登录，在 Exchange 中提示输入凭据 |
| 移动应用 |提示输入凭据 |提示输入凭据 |

如果已在任务 1 确定拥有第三方的 IdP，或者要使用 IdP 来提供与 Azure AD 的联合，需要注意以下支持的功能：

* 任何与 SP-Lite 配置文件兼容的 SAML 2.0 提供程序都支持对 Azure AD 和关联应用程序的身份验证
* 支持被动身份验证，方便完成 OWA、SPO 等的身份验证。
* 可通过 SAML 2.0 增强型客户端配置文件 (ECP) 支持 Exchange Online 客户端

此外，必须知道哪些功能不可用：

* 如果没有 WS-Trust/联合身份验证支持，其他所有活动客户端都会中断
  * 这意味着，Office 2016 以前的 Lync 客户端、OneDrive 客户端、Office 订阅、Office Mobile 都不可用
* Office 转换为被动身份验证可让它们支持纯 SAML 2.0 IdP，但支持仍然因客户端而异

> [!NOTE]
> 有关最新列表，请阅读文章 https://aka.ms/ssoproviders。
> 
> 

## <a name="define-synchronization-strategy"></a>定义同步策略
此任务将定义用于将组织的本地数据同步到云的工具，以及应该使用的拓扑。  由于大多数组织都使用 Active Directory，我们稍微详细提供有关使用 Azure AD Connect 来解决上述问题的信息。  针对没有 Active Directory 的环境，也提供有关使用 FIM 2010 R2 或 MIM 2016 来帮助规划此策略的信息。  但是，将来的 Azure AD Connect 版本将支持 LDAP 目录，因此，根据时间表，此信息可能有所帮助。

### <a name="synchronization-tools"></a>同步工具
多年来，已出现许多同步工具并用于各种方案。  在所有支持的方案中，Azure AD Connect 是目前首选的工具。  AAD 同步和 DirSync 也依然存在，甚至现在就在环境中。 

> [!NOTE]
> 有关每一项工具支持的功能的最新信息，请参阅[目录集成工具比较](active-directory-hybrid-identity-design-considerations-tools-comparison.md)一文。  
> 
> 

### <a name="supported-topologies"></a>支持的拓扑
定义同步策略时，必须确定使用的拓扑。 可以根据步骤 2 中已确定的信息，确定适合使用的拓扑。 单林单 Azure AD 拓扑最常见，它由单个 Active Directory 林和单个 Azure AD 实例组成。  这会用于大多数方案，也是在使用 Azure AD Connect Express 安装时预期的拓扑，如下图所示。

![](./media/hybrid-id-design-considerations/single-forest.png) 单林方案。在大型组织甚至小型组织中，拥有多个林很常见，如图 5 所示。

> [!NOTE]
> 有关不同的本地和 Azure AD 拓扑与 Azure AD Connect 同步的详细信息，请参阅 [Azure AD Connect 的拓扑](connect/active-directory-aadconnect-topologies.md)一文。
> 
> 

![](./media/hybrid-id-design-considerations/multi-forest.png) 

多林方案

如果是这样，当以下条件成立时，应该采用多林单 Azure AD 拓扑：

* 用户在所有林中只有 1 个标识 – 下面的唯一识别用户部分有更详细的说明。
* 用户向他们的标识所在的林进行身份验证
* UPN 和源定位点（固定 ID）来自此林
* Azure AD Connect 可访问所有林 – 这意味着不需要加入域，并且可以放入外围网络中（如果有利）。
* 用户只有一个邮箱
* 托管用户邮箱的林具有 Exchange 全局地址列表 (GAL) 中可见属性的最佳数据质量
* 如果用户没有邮箱，则任何林都可以用于提供这些值
* 如果有链接的邮箱，则在不同的林中还有另一个帐户用于登录。

> [!NOTE]
> 同时在本地和云中存在的对象将通过唯一标识符相互“关联”。 在目录同步的上下文中，此唯一标识符称为 SourceAnchor。 在单一登录的上下文中，它称为 ImmutableId。 [Azure AD Connect 的设计概念](connect/active-directory-aadconnect-design-concepts.md#sourceanchor)提供了有关使用 SourceAnchor 的更多注意事项。
> 
> 

如果有多个活动帐户或多个邮箱，Azure AD Connect 将选择其中一个并忽略其他的帐户或邮箱。  如果有链接的邮箱但没有其他帐户，则这些帐户不会导出到 Azure AD，并且用户将不是任何组的成员。  这不同以往在 DirSync 中的情况，主要是为了更充分支持这些多林方案。 下图显示了多林方案。

![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 

**多林多 Azure AD 方案**

在组织的 Azure AD 中，建议只有单个目录，但只要 Azure AD Connect 同步操作服务器与 Azure AD 目录之间保持 1:1 关系，则仍然支持。  对于每个 Azure AD 实例，都需要安装 Azure AD Connect。  此外，Azure AD 采用隔离设计，一个 Azure AD 实例中的用户看不到其他实例中的用户。

可以且支持将一个 Active Directory 本地实例连接到多个 Azure AD 目录，如下图所示：

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 

**单林筛选方案**

为此，以下条件必须成立：

* Azure AD Connect 同步服务器必须设置筛选，让它们都有一组对象的互斥集。  例如，将每个服务器的范围限定于特定域或 OU。
* DNS 域只能在单个 Azure AD 目录中注册，因此本地 AD 中的用户 UPN 必须使用独立的命名空间
* 一个 Azure AD 实例中的用户只能看到他们的实例中的用户。  他们看不到其他实例中的用户
* 只有一个 Azure AD 目录可以使用本地 AD 启用 Exchange 混合部署。
* 写回也有互斥性。  这使得此拓扑不支持部分写回功能，因为这些拓扑采用单个本地配置。  这包括：
  * 使用默认配置进行组写回
  * 设备写回

不支持以下功能，不应将其选作实现：

* 不支持将多个 Azure AD Connect 同步服务器连接到同一个 Azure AD 目录，即使它们已配置为同步对象的互斥集。
* 它不支持同步相同用户到多个 Azure AD 目录。 
* 也不支持进行配置更改，让一个 Azure AD 中的用户显示为另一个 Azure AD 目录中的联系人。 
* 也不支持将 Azure AD Connect 同步修改为连接到多个 Azure AD 目录。
* Azure AD 目录在设计上是隔离的。 它不支持将 Azure AD Connect 同步更改为从另一个 Azure AD 目录读取数据，以尝试在目录之间构建一般和统一的 GAL。 也不支持使用 Azure AD Connect 同步将用户导出为另一个本地 AD 的联系人。

> [!NOTE]
> 如果组织禁止网络上的计算机连接到 Internet，本文列出了在客户端计算机的输出允许列表和 Internet Explorer 信任的网站区域中应该添加的终结点（FQDN、IPv4 和 IPv6 地址范围），确保计算机可以成功使用 Office 365。 有关详细信息，请阅读 [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)。
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>定义多重身份验证策略
此任务将定义要使用的多重身份验证策略。  Azure 多重身份验证有两个不同的版本。  其中一个版本是基于云的身份验证，另一个版本是使用 Azure MFA 服务器进行本地身份验证。  根据上面所做的评估，可以确定哪一项是策略的正确解决方案。  使用下表确定哪个设计选项最符合公司的安全要求：

多重身份验证设计选项：

| 要保护的资产 | 云中的 MFA | 本地 MFA |
| --- | --- | --- |
| Microsoft 应用 |是 |是 |
| 应用库中的 SaaS 应用 |是 |是 |
| 通过 Azure AD 应用代理发布的 IIS 应用程序 |是 |是 |
| 不是通过 Azure AD 应用代理发布的 IIS 应用程序 |否 |是 |
| VPN、RDG 等远程访问系统 |否 |是 |

即使已选定策略的解决方案，仍然需要使用上述评估来确定用户位于何处。  这可能导致解决方案发生变化。  使用下表来帮助做出决定：

| 用户位置 | 首选设计选项 |
| --- | --- |
| Azure Active Directory |云中的多重身份验证 |
| Azure AD 和使用 AD FS 联合身份验证的本地 AD |两者 |
| Azure AD 和使用 Azure AD Connect 的本地 AD - 没有密码同步 |两者 |
| Azure AD 和使用 Azure AD Connect 的本地 AD - 有密码同步 |两者 |
| 本地 AD |多重身份验证服务器 |

> [!NOTE]
> 还应该确保选择的多重身份验证设计选项支持设计所需的功能。  有关详细信息，请参阅 [Choose the multi-factor security solution for you](../multi-factor-authentication/multi-factor-authentication-get-started.md#what-am-i-trying-to-secure)（选择适合自己的多因素安全解决方案）。
> 
> 

## <a name="multi-factor-auth-provider"></a>Multi-Factor Auth 提供程序
默认情况，具有 Azure Active Directory 租户的全局管理员可以使用多重身份验证。 但是，如果要将多重身份验证扩展到所有用户，并且/或者希望全局管理员能够利用管理门户、自定义问候语和报告等功能，必须购买并配置多重身份验证提供程序。

> [!NOTE]
> 还应该确保选择的多重身份验证设计选项支持设计所需的功能。 
> 
> 

## <a name="next-steps"></a>后续步骤
[确定数据保护要求](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>另请参阅
[设计注意事项概述](active-directory-hybrid-identity-design-considerations-overview.md)

