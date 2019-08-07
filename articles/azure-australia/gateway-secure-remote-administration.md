---
title: Azure 澳大利亚的网关远程管理安全
description: 有关在澳大利亚地区配置安全远程管理的指南, 以满足澳大利亚政府政策、法规和法律的特定要求。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 1e4c4712312faf2274a4a0737c4fc1f7ce39f98e
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824191"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Azure 澳大利亚的网关远程管理安全

管理活动安全和控制的任何系统的可用性和完整性都非常重要。 管理活动应在安全的设备中通过安全连接来完成, 并通过强身份验证和授权过程获得支持。 安全远程管理可确保仅执行授权的操作, 并且仅由已授权的管理员执行。

本文介绍如何为 Azure 中托管的可访问 internet 的系统实施安全的远程管理功能, 该系统与澳大利亚网络安全中心 (ACSC) 使用者指南和 ACSC 信息的意图相符。安全手册 (ISM)。

## <a name="australian-cyber-security-centre-acsc-requirements"></a>澳大利亚网络安全中心 (ACSC) 要求

在 ISM 中定义了英联邦系统的整体安全要求。 为了帮助提供安全管理的 ACSC, 该公司已发布[ACSC 保护:安全管理](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

本文档介绍安全管理的重要性, 并建议实现安全管理环境的一种方法。 本文档介绍安全管理解决方案的元素, 如下所示:

|元素   |描述   |
|---|---|
|特权访问控制   |控制特权帐户的访问权限是一种基本的安全控制, 可保护特权帐户不被滥用。 访问控制方法将包含 "最小特权" 和 "需要使用" 的概念, 以及用于管理服务帐户和员工变动的过程和步骤。   |
|多重身份验证   |除了用户名和密码短语 (如物理令牌或智能卡) 以外, 还可以实现身份验证的其他因素, 有助于保护关键资产。 如果攻击者损害了特权帐户的凭据, 那么, 所有管理操作都需要经过某种形式的多重身份验证, 因此可能会大大降低后果。|
|特权工作站|如果对管理任务使用已知的安全环境, 可能会导致网络因实现其他安全控制而遭到破坏的风险。|
|日志记录和审核   |自动生成、收集和分析来自工作站、服务器、网络设备和跳转框的安全与管理相关的事件将启用检测和尝试的折衷。 自动化使组织能够更快地做出响应, 从而降低泄露的影响。|
|网络分段和隔离|将网络划分为多个逻辑区域 (例如不同的安全域), 并通过限制从一个区域到另一个区域的数据类型来进一步分离这些逻辑网络, 限制横向移动。 分段可防止攻击者获取对其他资源的访问权限。|
|跳转框|跳转框是强制的远程访问服务器, 通常 utilising Microsoft 的远程桌面服务或安全外壳 (SSH) 软件。 对于通过从专用主机执行的所有管理操作, 访问关键系统的管理员, 跳转框作为步进点。|

本文提供了有关如何使用上述元素来安全管理在 Azure 中部署的系统的参考体系结构。

## <a name="architecture"></a>体系结构

若要提供安全的管理功能, 需要多个组件, 它们共同构成一个共同的解决方案。 在提供的参考体系结构中, 组件映射到 ACSC 中[所述的元素:安全管理](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

![Azure 安全远程管理体系结构](media/remote-admin.png)

## <a name="components"></a>组件

该体系结构的设计目的是确保仅为特权帐户授予必要的权限, 进行安全标识, 然后仅通过安全通信提供对管理接口的访问权限控制和审核的机制。

|解决方案| 组件|元素|
|---|---|---|
|安全设备 |<ul><li>特权工作站</li><li>移动设备</li><li>Microsoft Intune</li><li>组策略</li><li>跳转服务器/堡垒主机</li><li>实时 (JIT) 管理</li></ul> |<ul><li>特权工作站</li><li>跳转框</li></ul>|
|安全通信 |<ul><li>Azure 门户</li><li>Azure VPN 网关</li><li>远程桌面 (RD) 网关</li><li>网络安全组 (NSG)</li></ul> |<ul><li>网络分段和隔离</li></ul>|
|强身份验证 |<ul><li>域控制器 (DC)</li><li>Azure Active Directory (Azure AD)</li><li>网络策略服务器 (NPS)</li><li>Azure MFA</li></ul> |<ul><li>多重身份验证</li></ul> |
|强授权 |<ul><li>Identity and Access Management (IAM)</li><li>Privileged Identity Management (PIM)</li><li>条件访问</li></ul>|<ul><li>特权访问控制</li></ul>|
|||

>[!NOTE]
>有关日志记录和审核元素的详细信息, 请参阅有关[网关日志记录、审核和可见性](gateway-log-audit-visibility.md)的文章

## <a name="administration-workflow"></a>管理工作流

在 Azure 中部署的管理系统分为两个不同的类别, 即管理 Azure 配置和管理在 Azure 中部署的工作负荷。 Azure 配置是通过 Azure 门户执行的, 工作负荷管理是通过管理机制 (如远程桌面协议 (RDP)、安全外壳 (SSH) 或 PaaS 功能 (使用 SQL Management Studio 等工具) 完成的。

获取对管理的访问权限是一个多步骤过程, 其中包括体系结构中列出的组件, 并要求访问 Azure 门户和 Azure 配置, 然后才能对 Azure 工作负载进行访问。

>[!NOTE]
> 此处所述的步骤是使用 Azure 的图形用户界面 (GUI) 组件的一般过程。 还可以使用其他接口 (如 PowerShell) 完成这些步骤。

### <a name="azure-configuration-and-azure-portal-access"></a>Azure 配置和 Azure 门户访问

|步骤 |描述 |
|---|---|
|特权工作站登录 |管理员使用管理凭据登录特权工作站。 组策略控制可防止非管理性帐户向特权工作站进行身份验证, 并阻止管理帐户对非特权工作站进行身份验证。 Microsoft Intune 管理特权工作站的符合性, 以确保它是最新的软件补丁、反恶意软件和其他合规性要求。 |
|Azure 门户登录 |管理员打开使用传输层安全性 (TLS) 加密的 Azure 门户的 web 浏览器, 并使用管理凭据登录。 身份验证请求通过 Azure Active Directory 直接或通过身份验证机制 (如 Active Directory 联合身份验证服务 (AD FS) 或传递身份验证) 进行处理。 |
|Azure MFA |Azure MFA 向特权帐户的注册移动设备发送身份验证请求。 移动设备由 Intune 管理, 以确保符合安全要求。 管理员必须先使用 PIN 或生物识别系统对移动设备进行身份验证, Microsoft Authenticator 然后才能使用 PIN 或生物识别系统对 Azure MFA 进行身份验证。 |
|条件访问 |条件性访问策略检查身份验证尝试, 以确保它满足必要的要求, 例如连接的 IP 地址、特权帐户的组成员身份, 以及的管理和符合性状态Intune 报告的特权工作站。 |
|Privileged Identity Management (PIM) |通过 Azure 门户管理员现在可以为其授权到 PIM 的特权角色激活或请求激活。 PIM 确保特权帐户不具有任何持续的管理权限, 并且仅针对执行管理任务所需的时间的所有特权访问请求。 PIM 还为审核目的提供了所有请求和激活的日志记录。 |
|标识和访问管理|安全标识了特权帐户并激活角色后, 管理员便可以访问通过标识和访问管理向他们分配了权限的 Azure 订阅和资源。|

如果特权帐户完成了获取 Azure 门户的管理访问权限, 则可以配置对工作负荷的访问, 并可以进行管理连接。

### <a name="azure-workload-administration"></a>Azure 工作负载管理

|步骤 |描述|
|---|---|
|实时 (JIT) 访问|若要获取对虚拟机的访问权限, 管理员可以使用 JIT 从 RD 网关 IP 地址和 RDP 或 SSH 将 RDP 从跳转服务器请求到相关工作负荷虚拟机的访问权限。|
|Azure VPN 网关|管理员现在建立从其特权工作站到 Azure VPN 网关的点到站点 IPSec VPN 连接, 该连接执行证书身份验证以建立连接。|
|RD 网关|管理员现在使用远程桌面连接配置中指定的 RD 网关尝试与跳转服务器建立 RDP 连接。 RD 网关具有可通过 Azure VPN 网关连接访问的专用 IP 地址。 RD 网关上的策略控制是否授权特权帐户访问请求的跳转服务器。 RD 网关提示管理员输入凭据, 并将身份验证请求转发到网络策略服务器 (NPS)。|
|网络策略服务器 (NPS)|NPS 从 RD 网关接收身份验证请求, 并根据 Active Directory 验证用户名和密码, 然后将请求发送到 Azure Active Directory 以触发 Azure MFA 身份验证请求。|
|Azure MFA|Azure MFA 向特权帐户的注册移动设备发送身份验证请求。 移动设备由 Intune 管理, 以确保符合安全要求。 管理员必须先使用 PIN 或生物识别系统对移动设备进行身份验证, Microsoft Authenticator 然后才能使用 PIN 或生物识别系统对 Azure MFA 进行身份验证。|
|跳转服务器|成功通过身份验证后, 会使用传输层安全性 (TLS) 对 RDP 连接进行加密, 然后通过加密 IPSec 隧道发送到 Azure VPN 网关, 通过 RD 网关和转到跳转服务器。 在跳转服务器上, 管理员现在可以通过 RDP 或 SSH 连接到 JIT 请求中指定的虚拟机。|

## <a name="general-guidance"></a>通用指南

在实现本文中列出的组件时, 以下常规指南适用:

* 验证服务的区域可用性, 确保所有数据都保留在授权位置, 并将其部署到 AU Central 或 AU 中心 2, 作为受保护工作负荷的第一个首选项

* 请参阅*AZURE ACSC 认证报告-受保护的 2018*发布, 了解各个服务的认证状态, 并根据*ACSC 使用者指南在未包括在报告中的任何相关组件上执行自我评估–受保护 Microsoft Azure*

* 确保访问必要的身份验证组件 (例如 Azure AD、ADFS 和 PTA) 的网络连接和任何必需的代理配置

* 使用 Azure 策略来监视和强制实施符合性要求

* 请确保虚拟机 (尤其是 Active Directory 域控制器) 存储在加密的存储帐户中, 并利用 Azure 磁盘加密

* 创建并维护强健的标识和管理特权管理过程, 并管理支撑本文中列出的技术控制

|Resource|URL|
|---|---|
|澳大利亚法规和政策符合性文档|[澳大利亚法规和政策符合性文档](https://aka.ms/au-irap)|
|Azure 产品-澳大利亚地区和非区域|[Azure 产品-澳大利亚地区和非区域](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|缓解网络安全事件的策略|[缓解网络安全事件的策略](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC 保护:安全管理|[ACSC 保护:安全管理](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|如何：使用网络策略服务器 (NPS) 扩展和 Azure AD 集成远程桌面网关基础结构|[将 RD 网关与 NPS 和 Azure AD 集成](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>组件指南

本部分提供了有关每个组件及其在总体安全远程管理体系结构中的作用的信息。 提供了其他链接来访问有用的资源, 例如参考文档、指南和教程。

## <a name="secure-devices"></a>安全设备

特权用户用于执行管理功能的物理设备是恶意执行组件的重要目标。 维护物理设备的安全性和完整性, 并确保它们不受恶意软件的威胁, 保护它们免受危害是提供安全的远程管理功能的关键部分。 这涉及 ACSC 的基本8个策略中指定的高优先级安全配置, 以缓解应用程序允许列表、修补应用程序、应用程序强化和修补操作系统等网络安全事件。 必须对这些功能进行安装、配置、审核、验证和报告, 以确保设备的状态符合组织的要求。

### <a name="privileged-workstation"></a>特权工作站

特权工作站是一个强化的计算机, 可用于执行管理任务, 并且仅可用于管理帐户。 特权工作站应有适当的策略和配置, 以限制可以运行的软件、对网络资源的访问和 internet, 以及在设备被盗或受到威胁的情况下应保护凭据。 |

|资源|链接|
|---|---|
|特权访问工作站体系结构概述|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|保护特权访问参考资料|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|

### <a name="mobile-device"></a>移动设备

移动设备由于其可移植性和大小的增加而导致意外丢失或被盗的风险, 并且需要相应地进行保护。 该移动设备为身份验证提供了一种更强的额外因素, 旨在强制实现对设备访问的身份验证、通过定位服务的可跟踪性、加密功能, 以及远程擦除功能。 使用移动设备作为 Azure 的附加身份验证因素时, 应将设备配置为将 Microsoft Authenticator 应用与 PIN 或生物识别身份验证配合使用, 而不是通过电话呼叫或短信使用。

|资源|链接|
|---|---|
|Azure AD 身份验证方法|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|如何使用 Microsoft Authenticator 应用程序|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune 是企业移动性 + 安全性的组件, 用于管理移动设备和应用。 它与其他组件密切集成, 如用于标识和访问控制的 Azure Active Directory 和用于数据保护的 Azure 信息保护。 Intune 为工作站和移动设备提供了策略来设置访问资源的符合性要求, 并提供了报告和审核功能来深入了解管理设备的状态。

|资源|链接|
|---|---|
|Microsoft Intune 文档|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Intune 中的设备符合性入门|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|

### <a name="group-policy"></a>组策略

组策略用于控制操作系统和应用程序的配置。 安全策略控制系统的身份验证、授权和审核设置。 组策略用于强化特权工作站、保护管理凭据和限制非特权帐户访问特权设备。

|资源|链接|
|---|---|
|允许本地登录组策略设置|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|

### <a name="jump-server--bastion-host"></a>跳转服务器/堡垒主机

跳转服务器/堡垒主机是一个集中式的管理点。 它具有执行管理任务所需的工具, 但也具有连接到管理端口上的资源所需的网络访问权限。 跳转服务器是管理本文中虚拟机工作负荷的中心点, 但也可以将其配置为管理平台即服务 (PaaS) 功能 (如 SQL) 的授权点。 使用标识和网络控件, 可以基于每个服务限制对 PaaS 功能的访问。

|资源|链接|
|---|---|
|实现安全的管理主机|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|

### <a name="just-in-time-jit-access"></a>实时 (JIT) 访问

JIT 是一个 Azure 安全中心功能, 它使用网络安全组 (Nsg) 来阻止对虚拟机上的 RDP 和 SSH 等管理协议的访问。 在虚拟机上托管的应用程序将继续正常工作, 但要获取的管理访问权限, 只能在设定的时间段内授予。 出于审核目的, 将记录所有请求。

|资源 |链接 |
|---|---|
|管理实时 (JIT) 访问|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|自动执行 Azure 实时 VM 访问|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|

## <a name="secure-communication"></a>安全通信

管理活动的通信流量可以包含高度敏感的信息, 例如管理凭据, 并且必须相应地进行管理和保护。 提供安全的通信涉及到一种可靠的加密功能, 以防止窃听和网络分离, 以及将管理流量限制到授权终结点的限制, 并在系统受到损害时控制横向移动。

### <a name="azure-portal"></a>Azure 门户

与 Azure 门户的通信使用传输层安全性 (TLS) 进行加密, 而 Azure 门户已通过 ACSC 的认证。 英联邦实体应遵循*ACSC 使用者指南*中的建议, 并配置其 web 浏览器, 以确保它们使用最新版本的 TLS 和受支持的加密算法。

|资源 |链接 |
|---|---|
|Azure 加密概述–传输中的加密|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|

### <a name="azure-vpn-gateway"></a>Azure VPN 网关

Azure VPN 网关提供从特权工作站到 Azure 的安全加密连接。 Azure VPN 网关已通过 ACSC 的认证, 用于提供安全 IPSec 通信。 英联邦实体应按照 ACSC 使用者指南、ACSC 认证报告和其他特定指导配置 Azure VPN 网关。

|资源 |链接 |
|---|---|
|关于点到站点连接|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Azure VPN 网关加密详细信息|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Azure VPN 网关配置|[Azure VPN 网关配置](vpn-gateway.md)|

### <a name="remote-desktop-rd-gateway"></a>远程桌面 (RD) 网关

RD 网关是一种安全机制, 用于控制和 authorising 与系统的 RDP 连接。 它的工作原理是将 RDP 流量封装为超文本传输协议 (HTTPS), 并使用 TLS 进行加密。 TLS 为管理通信提供额外的安全层。

|资源 |链接 |
|---|---|
|远程桌面服务体系结构|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|

### <a name="network-security-groups-nsgs"></a>网络安全组 (NSG)

用于进入或离开子网或虚拟机的网络流量的访问控制列表 (Acl) 的 Nsg 函数。 Nsg 提供网络分段, 并提供一种机制来控制和限制系统之间允许的通信流。 Nsg 是实时管理 (JIT) 的核心组件, 用于允许或拒绝对管理协议的访问。

|资源 |链接 |
|---|---|
|Azure 安全组概述|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|如何：规划虚拟网络|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|

## <a name="strong-authentication"></a>强身份验证

在授予对系统的访问权限之前安全标识特权用户是安全管理的核心组件。 必须准备好机制来保护与特权帐户关联的凭据, 并防止恶意执行组件通过模拟或凭据被盗获取对系统的访问权限。

### <a name="domain-controller-dc"></a>域控制器 (DC)

在高级别上, DC 承载 Active Directory 数据库的副本, 该副本包含域中的所有用户、计算机和组。 DCs 为用户和计算机执行身份验证。 此体系结构中的 Dc 作为虚拟机托管在 Azure 中, 并为连接到 "跳转服务器" 和 "工作负荷" 虚拟机的特权帐户提供身份验证服务。

|资源 |链接 |
|---|---|
|Active Directory 域服务概述|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD 是 Azure 的身份验证服务。 它包含云

标识并为 Azure 环境提供身份验证和授权。 Azure AD 可以通过 Azure AD Connect 与 Active Directory 同步, 并可通过 Active Directory 联合身份验证服务 (AD FS) 和 Azure AD Connect 提供联合身份验证。 Azure AD 是安全管理的核心组件。

|资源 |链接 |
|---|---|
|Azure Active Directory 文档|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|混合标识文档|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|

### <a name="network-policy-server-nps"></a>网络策略服务器 (NPS)

NPS 是一种身份验证和策略服务器, 可提供高级身份验证和授权进程。 提供此体系结构中的 NPS 服务器是为了将 Azure MFA 身份验证与 RD 网关 authentication 请求相集成。 NPS 具有特定的插件, 可在 Azure AD 中支持与 Azure MFA 集成。

|资源 |链接 |
|---|---|
|网络策略服务器文档|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|

### <a name="azure-mfa"></a>Azure MFA

Azure MFA 是 Azure Active Directory 中提供的一种身份验证服务, 用于在访问云资源 (如 Azure 门户) 以外的用户名和密码上启用身份验证请求。 Azure MFA 支持多种身份验证方法, 此体系结构使用 Microsoft Authenticator 应用程序, 以增强安全性并与 NPS 集成。

|资源 |链接 |
|---|---|
|工作原理：Azure 多重身份验证|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|如何：部署基于云的 Azure 多重身份验证|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|

## <a name="strong-authorisation"></a>强授权

安全标识了特权帐户后, 可以授予其对资源的访问权限。 授权控制和管理分配给特定帐户的权限。 强大的授权流程与 ACSC 的基本8个策略保持一致, 以减少限制管理特权的网络安全事件。

### <a name="identity-and-access-management"></a>标识和访问管理

在 Azure 中执行特权操作的访问权限基于分配给该帐户的角色。 Azure 包含具有特定权限的广泛、精细的角色, 可用于进行特定任务。 可以在多个级别 (如订阅或资源组) 授予这些角色。 角色分配和权限管理基于 Azure Active Directory 中的帐户和组, 并通过 Azure 中的访问控制 (IAM) 进行管理。

|资源 |链接 |
|---|---|
|Azure 基于角色的访问控制|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|了解角色定义|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM 是控制特权角色访问权限的 Azure Active Directory 组件。 特权帐户不需要永久或长期的特权访问权限, 但可以被授予在一段时间内请求特权访问以完成特权活动的能力。 PIM 提供了有关维护和限制特权访问的其他控制, 以及用于跟踪特权使用实例的日志记录和审核。

|资源 |链接 |
|---|---|
|Privileged Identity Management (PIM) 文档|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|开始使用 PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|

### <a name="conditional-access"></a>条件访问

条件性访问是 Azure Active Directory 的一个组件, 它根据条件允许或拒绝访问资源。 这些条件可以是基于网络位置、设备类型、合规性状态、组成员身份等。 条件性访问用于通过 Intune 和管理帐户的组成员身份强制实施 MFA、设备管理和合规性。

|资源 |链接 |
|---|---|
|条件访问文档|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|如何：需要将托管设备用于具有条件访问的云应用访问权限|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|

## <a name="next-steps"></a>后续步骤

有关通过 Azure 中的网关组件控制流量的详细信息, 请查看有关[网关进入流量管理和控制](gateway-ingress-traffic.md)的文章。
