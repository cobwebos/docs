---
title: "增强在 Azure 中的远程管理安全性 |Microsoft 文档"
description: "本文讨论了用于管理 Microsoft Azure 环境，包括云服务、 虚拟机和自定义应用程序时增强远程管理安全的步骤。"
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 2431feba-3364-4a63-8e66-858926061dd3
ms.service: security
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2017
ms.author: terrylan
ms.openlocfilehash: bf4f0b64d1230395bf5dacc467d09debecdef559
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="security-management-in-azure"></a>在 Azure 中的安全管理
Azure 订阅服务器可能管理其云环境从多个设备，包括管理工作站、 开发人员电脑和具有特定于任务的权限甚至特权的最终用户设备。 在某些情况下，管理功能来执行通过基于 web 的控制台如[Azure 门户](https://azure.microsoft.com/features/azure-portal/)。 在其他情况下，可能有直接连接到 Azure 的本地系统从通过虚拟专用网络 (Vpn)、 终端服务、 客户端应用程序协议，或 （以编程方式） Azure 服务管理 API (SMAPI)。 此外，客户端终结点可以是两个域联接或隔离和非托管，例如平板电脑或智能手机。

尽管多个访问和管理功能提供一组丰富的选项，这种变化可以添加到云部署的重大风险。 它可能很难管理、 跟踪和审核管理操作。 这种变化还可能会带来通过发出无法控制访问用于管理云服务的客户端终结点的安全威胁。 使用常规或个人工作站用于开发和管理基础结构将打开不可预知的威胁方法，如 web 浏览 （例如，浇水漏洞攻击） 或 （例如，社会工程和网络钓鱼） 电子邮件。

![][1]

攻击的可能性会增加此类型的环境中，因为它一项挑战构造安全策略和机制，以适当地管理对 Azure 的访问接口 （如 SMAPI) 从广泛不同终结点。

### <a name="remote-management-threats"></a>远程管理威胁
通常，攻击者尝试破坏帐户凭据 （例如，通过密码暴力破解、 网络钓鱼和凭据搜集），或诱骗用户运行有害代码 （例如，从具有偷渡式下载的有害网站或有害的电子邮件附件） 获取特许访问权限。 在远程托管的云环境中，帐户漏洞可能导致由于随时随地访问增加的风险。

即使使用主管理员帐户的严格控制，可以使用较低级别的用户帐户以利用的安全策略中的漏洞。 缺乏相应的安全培训还可能导致违反通过意外泄漏或公开的帐户信息。

当用户工作站也用于管理任务时，它可能会泄漏的许多不同时间点。 是用户浏览 web，使用第三方和开放源代码工具，还是要打开有害文档文件，包含木马病毒。

一般情况下，最导致数据泄露的针对性的攻击可跟踪到浏览器漏洞、 插件 （如 Flash，PDF、 Java) 和桌面的计算机上的矛仿冒 （电子邮件）。 这些计算机可能具有管理级别或服务级权限才能访问实时服务器或网络设备的操作时用于开发或管理其他资产。

### <a name="operational-security-fundamentals"></a>操作安全基础知识
有关更安全的管理和操作，你可以尽量减少客户端的攻击面减少可能的入口点的数目。 这可以通过安全原则:"的职责分离"和"隔离的环境。"

隔离敏感函数从另一个以减少在一个级别上一个错误会导致在另一个漏洞的可能性。 示例：

* 不应与可能导致损坏 （例如，然后感染的基础结构服务器的管理员的电子邮件中的恶意软件） 的活动组合管理任务。
* 用于高敏感度操作工作站不应使用用于高风险的用途，例如浏览 Internet 的同一系统。

通过删除不必要的软件减少系统的攻击面。 例如：

* 标准管理、 支持或开发工作站如果应该不需要的电子邮件客户端或其他工作效率应用程序的安装设备的主要目的是用于管理云服务。

具有到基础结构组件的管理员访问权限的客户端系统应会受到严格可能的策略，以减少安全风险。 示例：

* 安全策略可以包括拒绝从设备和使用限制的防火墙配置打开的 Internet 访问的组策略设置。
* 如果需要直接访问，请使用 Internet 协议安全性 (IPsec) Vpn。
* 配置单独的管理和开发 Active Directory 域。
* 隔离并筛选管理工作站的网络流量。
* 使用反恶意软件。
* 实现多因素身份验证以降低凭据被盗的风险。

整合访问资源和消除的非托管终结点还简化了管理任务。

### <a name="providing-security-for-azure-remote-management"></a>提供有关 Azure 的远程管理的安全
Azure 提供了安全机制，以帮助管理 Azure 云服务和虚拟机的管理员。 这些机制包括：

* 身份验证和[基于角色的访问控制](../active-directory/role-based-access-control-configure.md)。
* 监视、 记录和审核。
* 证书和加密的通信。
* Web 管理门户。
* 网络数据包筛选。

使用客户端安全配置和管理网关的数据中心部署，就可以限制和监视云应用程序和数据的管理员访问权限。

> [!NOTE]
> 这篇文章中的某些建议可能会导致增加了数据、 网络或计算资源的使用量，并且还可能增加你的许可证或订阅成本。
>
>

## <a name="hardened-workstation-for-management"></a>强化管理工作站
强化工作站的目标是消除所有但最关键函数其所需进行操作，使潜在的攻击面越小越好。 系统强化包括最小化安装的服务和应用程序，限制应用程序执行，将网络访问权限限制为仅需要什么，以及始终保持最新系统。 此外，使用强化的工作站进行管理的承受程度分离管理工具和从其他最终用户任务的活动。

在本地企业环境中，你可以限制你通过专用的管理网络、 具有卡访问的服务器机房和在网络的受保护区域运行的工作站的物理基础结构的受攻击面。 在云或混合 IT 模型中，正在坚持不懈有关安全管理服务会更复杂由于缺少对 IT 资源的物理访问。 实现保护解决方案需要小心软件配置、 专注于安全的过程和全面的策略。

在锁定工作站中的最小特权最小化的软件占用空间用于云管理-以及有关应用程序开发 — 可以通过标准化的远程管理和开发环境降低安全事件的风险。 强制写入的工作站配置可以帮助防止这种损害的用于通过关闭由恶意软件和攻击的许多常见途径管理关键云资源的帐户。 具体而言，你可以使用[Windows AppLocker](http://technet.microsoft.com/library/dd759117.aspx)和 HYPER-V 技术来控制和隔离客户端系统行为和缓解威胁，包括电子邮件或浏览 Internet。

在强制写入的工作站上，管理员运行标准用户帐户 （这将阻止管理级别执行） 和关联的应用程序进行控制的允许列表。 强制写入工作站的基本元素如下所示：

* 扫描和修补，处于活动状态。 部署反恶意软件、 执行定期安全漏洞扫描和及时使用最新的安全更新来更新所有工作站。
* 有限的功能。 卸载任何应用程序，不需要禁用不必要 （启动后） 服务。
* 网络强化。 使用 Windows 防火墙规则以允许仅有效 IP 地址、 端口和 Url 与 Azure 的管理。 确保入站远程连接到工作站也被阻止。
* 执行限制。 允许只有一组预定义的可执行文件所需的管理来运行 （称为"默认拒绝"）。 默认情况下，用户应拒绝的权限来运行任何程序，除非进行显式定义在允许列表。
* 最小特权。 管理工作站用户不应在本地计算机本身上具有任何管理权限。 这样一来，他们无法更改系统配置或系统文件中，有意或无意中。

你可以通过使用强制所有这些[组策略对象](https://www.microsoft.com/download/details.aspx?id=2612)(Gpo) 在 Active Directory 域服务 (AD DS) 和通过 （本地） 管理域它们应用到所有的管理帐户。

### <a name="managing-services-applications-and-data"></a>管理服务、 应用程序和数据
Azure 云服务配置都被执行通过 Azure 门户或 SMAPI，通过 Windows PowerShell 命令行界面或定制的应用程序利用这些 RESTful 接口。 服务使用这些机制包括 Azure Active Directory (Azure AD)、 Azure 存储、 Azure 网站和 Azure 虚拟网络和其他人。

部署虚拟机 – 应用程序提供其自己的客户端工具和接口根据需要如 Microsoft 管理控制台 (MMC)、 企业管理控制台 （如 Microsoft System Center 或 Windows Intune） 或另一个管理应用程序-Microsoft SQL Server Management Studio，例如。 这些工具通常驻留在企业环境或客户端网络。 它们可能依赖于特定的网络协议，例如远程桌面协议 (RDP)，需要直接的有状态连接。 一些可能具有不应公开发布或可通过 Internet 访问的支持 web 的接口。

你可以通过限制对 Azure 中的基础结构和平台服务管理访问[多因素身份验证](../multi-factor-authentication/multi-factor-authentication.md)， [X.509 管理证书](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/)，和防火墙规则。 Azure 门户和 SMAPI 需要传输层安全 (TLS)。 但是，服务和应用程序将部署到 Azure 需要你采取根据你的应用程序具有适当的保护措施。 经常可以通过标准化强化的工作站配置更轻松地启用这些机制。

### <a name="management-gateway"></a>管理网关
若要集中所有管理访问权限和简化监视和日志记录，你可以部署专用[远程桌面网关](https://technet.microsoft.com/library/dd560672)本地网络，连接到 Azure 环境中的 （RD 网关） 服务器。

远程桌面网关是基于策略的 RDP 代理服务强制执行安全需求。 实现 RD 网关与 Windows Server 网络访问保护 (NAP) 可帮助确保仅符合特定的安全运行状况条件由 Active Directory 域服务 (AD DS) 组策略对象 (Gpo) 建立的客户端可以连接。 另外：

* 设置[Azure 管理证书](http://msdn.microsoft.com/library/azure/gg551722.aspx)在 RD 网关，以便它是唯一的主机可以访问 Azure 门户。
* 加入到同一个 RD 网关[管理域](http://technet.microsoft.com/library/bb727085.aspx)作为管理员工作站。 这是必需的当在一个域中具有单向信任到 Azure AD 中，使用站点到站点 IPsec VPN 或 ExpressRoute 或如果你要将凭据联合之间本地 AD DS 实例和 Azure AD。
* 配置[客户端连接授权策略](http://technet.microsoft.com/library/cc753324.aspx)以便验证客户端计算机名是否有效 （加入域） RD 网关，并且允许访问 Azure 门户。
* 使用用于 IPsec [Azure VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/)以进一步管理通信防止窃听和令牌被盗，或请考虑通过独立互联网链接[Azure ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/)。
* 启用多因素身份验证 (通过[Azure 多因素身份验证](../multi-factor-authentication/multi-factor-authentication.md)) 或智能卡登录通过 RD 网关的管理员的身份验证。
* 配置源[IP 地址限制](http://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/)或[网络安全组](../virtual-network/virtual-networks-nsg.md)中 Azure 允许的管理终结点的数量降至最低。

## <a name="security-guidelines"></a>安全指导原则
一般情况下，帮助保护安全管理员工作站，与云的使用是类似于用于任何工作站上本地的做法 — 例如，最小化的生成和限制的权限。 云管理某些唯一方面存在风格更像是企业远程或带外管理。 其中包括使用和的凭据、 安全性增强远程访问和威胁检测和响应的审核。

### <a name="authentication"></a>身份验证
你可以使用 Azure 登录限制来限制访问管理工具的源 IP 地址和审核访问请求。 为了帮助标识 （工作站和/或应用程序） 的管理客户端的 Azure，你可以配置 SMAPI （通过 Windows PowerShell cmdlet 如客户开发工具） 和 Azure 门户需要客户端管理证书以安装，除了 SSL 证书。 我们还建议管理员访问权限，需要多因素身份验证。

某些应用程序或将部署到 Azure 的服务可能有最终用户和管理员访问权限，他们自己的身份验证机制，而其他充分利用 Azure AD。 根据是否要联盟通过 Active Directory 联合身份验证服务 (AD FS) 的凭据，使用目录同步或维护仅在云中的用户帐户、 使用[Microsoft Identity Manager](https://technet.microsoft.com/library/mt218776.aspx) （Azure AD 高级版的一部分） 可帮助你管理标识资源之间的生命周期。

### <a name="connectivity"></a>连接
几种机制都可用于帮助到你的 Azure 虚拟网络的安全客户端连接。 两种机制，[站点到站点 VPN](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (S2S) 和[点到站点 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) (P2S)，允许使用行业标准 IPsec (S2S) 或[安全套接字隧道协议](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx)(SSTP) (P2S) 进行加密和隧道。 当 Azure 连接到的面向公众的 Azure 服务管理，例如 Azure 门户时，Azure 将要求安全超文本传输协议 (HTTPS)。

不会连接到 Azure 通过 RD 网关的独立强化的工作站应基于 SSTP 的点到站点 VPN 用于创建到 Azure 虚拟网络中的初始连接，然后建立到从具有 VPN 隧道的单个虚拟机的 RDP 连接。

### <a name="management-auditing-vs-policy-enforcement"></a>管理审核策略实施与
通常情况下，有两种方法用于帮助保护管理过程： 审核和策略实施。 执行操作同时提供全面的控件，但可能无法在所有情况下。 此外，每种方法具有不同级别的风险、 成本，以及与管理安全性，尤其当它与相关的个人和系统体系结构中放置的信任级别相关联的工作量。

监视、 记录和审核提供跟踪和了解管理活动的基础，但它可能不是始终都可行审核中完整的详细信息，由于生成的数据量的所有操作。 但是，审核的管理策略的有效性是最佳做法是。

包括严格的访问控制的策略强制将编程机制集中放可以控制管理员操作的位置，并帮助确保正在使用所有可能的保护措施。 日志记录提供的强制，除了的谁做了什么、 从何处，以及何时记录的证据。 日志记录还使你能够审核和交叉检查信息有关管理员如何遵循策略，并且它提供的活动的证据

## <a name="client-configuration"></a>客户端配置
我们建议强化工作站的三种主配置。 它们之间的最大区别是成本、 可用性和可访问性，同时在所有选项中维护一个类似的安全配置文件。 下表提供的好处和风险到每个的短分析。 （请注意，"企业 PC"是指将为所有域用户，而不考虑角色部署的标准桌面电脑配置。）

| 配置 | 优势 | Cons |
| --- | --- | --- |
| 强化的独立工作站 |严格控制的工作站 |成本较高的专用桌面 |
| - | 减少应用程序攻击的风险 |提高了的管理工作量 |
| - | 明确职责分离 | - |
| 作为虚拟机的企业 PC |减少了的硬件成本 | - |
| - | 职责划分的角色和应用程序 | - |
| Windows to go 的 BitLocker 驱动器加密 |与大多数 Pc 中的兼容性 |资产跟踪 |
| - | 成本效益和可移植性 | - |
| - | 独立的管理环境 |- |

很重要强化的工作站主机和不来宾，主机操作系统和硬件之间没有任何内容。 遵照"清理源原则"（也称为"安全源"），则意味着主机应最强化。 否则，强化的工作站 （来宾） 受到攻击系统上托管它。

你可以进一步隔离通过对每个强化工作站仅工具的专用的系统映像管理功能和权限需要用于管理选择 Azure 和云应用程序，具有特定的本地 AD DS Gpo 必需的任务。

针对本地基础结构的 IT 环境 （例如，不能访问本地 AD DS 实例 gpo 由于所有服务器都都位于云中），如服务[Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx)可以简化部署和维护工作站配置。

### <a name="stand-alone-hardened-workstation-for-management"></a>管理的的独立强化的工作站
提供独立的强制写入工作站，管理员具有电脑或用于管理任务和另一个单独的 PC 的便携式计算机或便携式计算机的非管理任务。 专用于管理你的 Azure 服务的工作站不需要安装其他应用程序。 此外，使用支持的工作站[信任的平台模块](https://technet.microsoft.com/library/cc766159)(TPM) 或类似设备身份验证和某些攻击防止在硬件级别加密技术帮助。 TPM 还可以通过使用支持的系统驱动器的整卷保护[BitLocker 驱动器加密](https://technet.microsoft.com/library/cc732774.aspx)。

在独立强化的工作站方案中 （如下所示），Windows 防火墙 （或非 Microsoft 客户端防火墙） 的本地实例配置为阻止入站的连接，例如 RDP。 管理员可以登录到强化工作站和启动建立 VPN 连接与 Azure 虚拟网络，但不能登录到公司的 PC 上并使用 RDP 连接到强制写入工作站本身后连接到 Azure 的 RDP 会话。

![][2]

### <a name="corporate-pc-as-virtual-machine"></a>作为虚拟机的企业 PC
在其中单独的独立强化的工作站成本高昂或很不方便的情况下，强制写入的工作站可以托管的虚拟机执行非管理任务。

![][3]

若要避免可能会出现一个工作站用于系统管理和其他的日常工作任务从多个安全风险，可以将 Windows HYPER-V 虚拟机部署到强化的工作站。 作为企业的电脑，可以使用此虚拟机。 公司的 PC 环境可以保持独立于主机，从而减少了其攻击面并从与敏感的管理任务共存中删除用户的每日活动 （如电子邮件）。

公司的 PC 虚拟机在受保护的空间中运行，并提供用户应用程序。 主机保持"干净源"，并强制执行严格的网络策略在根操作系统 （例如，从虚拟机阻塞 RDP 访问） 中。

### <a name="windows-to-go"></a>Windows To Go
为要求强化的独立工作站的另一种方法是使用[Windows To Go](https://technet.microsoft.com/library/hh831833.aspx)驱动器中，一种功能，支持客户端 USB 启动功能。 Windows To Go 使用户能够启动到隔离的系统映像从加密的 USB 闪存驱动器运行兼容的 PC。 它为远程管理终结点上提供其他控件，因为映像可以完全管理的公司的 IT 组中，然后使用严格的安全策略，最小的 OS 内部，并且 TPM 支持。

在下图中，可移植图像是预配置为仅连接到 Azure，已加入域的系统要求多因素身份验证，并且会阻止所有非管理流量。 如果用户启动相同 PC 至标准公司映像，并尝试访问 RD 网关的 Azure 管理工具，会话的阻塞。 Windows To Go 成为根级别操作系统，并且没有其他层所需 （主机操作系统、 虚拟机监控程序、 虚拟机），可能会更容易外部攻击。

![][4]

请务必请注意，更轻松地丢失比平均的桌面 PC USB 闪存驱动器。 使用 BitLocker 加密整个卷，以及强密码，使不太可能的攻击者可以使用用于有害目的的驱动器映像。 此外，如果 U 盘丢失，撤消和[颁发新的管理证书](https://technet.microsoft.com/library/hh831574.aspx)以及快速密码重置可以降低风险。 管理审核日志驻留在 Azure 中，不在客户端，进一步减少数据丢失。

## <a name="best-practices"></a>最佳做法
当你管理应用程序和在 Azure 中的数据，请考虑以下其他指导原则。

### <a name="dos-and-donts"></a>注意事项
不假定因为工作站已被锁定，不需要其他常见的安全要求，需满足。 潜在的风险更高版本，这是管理员帐户通常具有提升的访问权限级别的。 下表中显示了风险和其备用的安全实践的示例。

| 禁止事项 | 要求事项 |
| --- | --- |
| 不电子邮件的管理员访问权限或其他机密信息 （例如，SSL 或管理证书） 凭据 |通过提供帐户名和密码通过语音 （但不是将它们存储在语音邮件），执行 （通过加密的会话） 的客户端/服务器证书的远程安装中保持机密性、 下载从受保护的网络共享，或通过可移动媒体手动分发。 |
| - | 主动管理你的管理证书的生命周期。 |
| 不要将存储帐户密码未加密或在应用程序存储 （如电子表格、 SharePoint 站点或文件共享） 未经过哈希处理。 |建立安全管理原则和系统采用强化策略，并将其应用到你的开发环境。 |
| - | 使用[增强的缓解体验工具包 5.5](https://technet.microsoft.com/security/jj653751)证书钉住的规则，以确保对 Azure SSL/TLS 站点的正确访问。 |
| 请不要共享帐户和密码管理员，之间或跨多个用户帐户或服务，特别是那些社交媒体或其他非管理活动中重复使用密码。 |创建专用的 Microsoft 帐户以管理你的 Azure 订阅 — 不用于个人电子邮件的帐户。 |
| 不发送电子邮件配置文件。 |配置文件和配置文件应安装来自受信任的来源 （例如，加密 USB 闪存驱动器），不是从一种机制，可以轻松地受到影响，例如电子邮件。 |
| 不要使用弱或简单登录密码。 |强制实施强密码策略，过期周期 （changeon-首次使用），控制台超时和自动帐户锁定。 使用多因素身份验证的客户端密码管理系统用于密码保管库访问权限。 |
| 不公开到 Internet 的管理端口。 |锁定 Azure 端口和 IP 地址以限制管理访问权限。 有关详细信息，请参阅[Azure 网络安全](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx)白皮书。 |
| - | 对所有管理连接中使用防火墙、 Vpn 和 NAP。 |

## <a name="azure-operations"></a>Azure 操作
在 Microsoft 的操作的 Azure、 操作工程师和支持人员访问 Azure 的生产系统使用[强化 Vm 的工作站 Pc](#stand-alone-hardened-workstation-for-management)对企业内部网络访问和应用程序 （例如电子邮件、 intranet，等等。） 在其上设置。 所有管理工作站计算机都具有 Tpm、 主机启动驱动器是使用 BitLocker 加密，但它们 Microsoft 的主企业域中加入到特殊的组织单位 (OU)。

通过组策略集中式的软件更新使用情况下，系统强化是强制执行。 有关审核和分析中，事件日志 （如安全和 AppLocker） 从管理工作站收集并保存到一个中心位置。

此外，专用跳转盒 Microsoft 的网络上需要双因素身份验证用于连接到 Azure 的生产网络。

## <a name="azure-security-checklist"></a>Azure 安全清单
将管理员可以强制写入的工作站执行的任务数降至最低，可帮助最大程度减少开发和管理环境中的攻击面。 使用以下技术来帮助保护你强化的工作站：

* IE 强化。 Internet Explorer 浏览器 （或任何 web 浏览器中的，其实） 是由于与外部服务器其广泛交互的有害代码的密钥的入口点。 查看你的客户端策略并强制执行保护模式中运行、 禁用外接程序、 禁用文件下载和使用[Microsoft SmartScreen](https://technet.microsoft.com/library/jj618329.aspx)筛选。 确保显示安全警告。 充分利用 Internet 区域，并创建一份已为其配置合理强化的受信任的站点。 阻止所有其他站点和在浏览器代码，例如 ActiveX 和 Java。
* 标准用户。 运行标准用户带来了许多优点，其中最大是窃取通过恶意软件的管理员凭据变得非常困难。 此外，标准用户帐户没有根操作系统中，提升的权限和许多配置选项和 Api 锁定默认情况下。
* AppLocker。 你可以使用[AppLocker](http://technet.microsoft.com/library/ee619725.aspx)程序和用户可以运行的脚本进行限制。 你可以在审核或强制模式下运行 AppLocker。 默认情况下，AppLocker 必须允许用户管理员令牌，客户端上运行的所有代码的允许规则。 此规则存在阻止管理员锁定本身 out，并且它仅适用于提升令牌。 另请参阅代码完整性作为 Windows 服务器的一部分[核心安全](http://technet.microsoft.com/library/dd348705.aspx)。
* 代码签名。 代码签名所有工具和脚本使用由管理员提供了部署应用程序锁定策略的可管理机制。 哈希值不能扩展快速发生更改的代码，并且文件路径不提供高级别的安全性。 应将 AppLocker 规则与 PowerShell 组合[执行策略](http://technet.microsoft.com/library/ee176961.aspx)，仅允许特定签名的代码和脚本要[执行](http://technet.microsoft.com/library/hh849812.aspx)。
* 组策略。 创建一个全局的管理策略，将应用于任何域的工作站，用于管理 （和从所有其他用户的阻止访问），以及在这些工作站身份验证的用户帐户。
* 增强了安全性的设置。 保护您的基线强化的工作站映像，以帮助防止篡改。 使用安全措施，如加密和隔离来存储映像、 虚拟机和脚本，并限制访问 （可能是使用可审核检查-在/签出进程）。
* 修补。 维护一致的生成 （或具有用于开发、 操作和其他管理任务的单独映像），扫描恶意软件和更改例行，保持生成最新，并仅在需要时激活机。
* 加密。 请确保管理工作站拥有 TPM 才能更安全地使[加密文件系统](https://technet.microsoft.com/library/cc700811.aspx)(EFS) 和 BitLocker。 如果你使用 Windows To Go，加密有仅 USB 密钥与 BitLocker 一起使用
* 监管。 使用 AD DS Gpo 来控制所有管理员的 Windows 接口，如共享文件。 审核、 监视和日志记录进程中包括管理工作站。 跟踪所有管理员和开发人员访问和使用情况。

## <a name="summary"></a>摘要
用于管理 Azure 云服务、 虚拟机和应用程序使用强化的工作站配置可以帮助你避免很多风险和可以来自远程管理关键的 IT 基础结构的威胁。 Azure 和 Windows 提供用来帮助保护和控制通信、 身份验证和客户端行为，可以采用的机制。

## <a name="next-steps"></a>后续步骤
以下资源可用于提供有关 Azure 的更多常规信息以及相关的 Microsoft 服务，以及本白皮书中所引用的特定项：

* [保护特权访问](https://technet.microsoft.com/library/mt631194.aspx)– 用于设计和构建 Azure 管理的安全管理工作站获取技术详细信息
* [Microsoft 信任中心](https://www.microsoft.com/TrustCenter/Security/AzureSecurity)-了解有关保护 Azure 结构和在 Azure 运行的工作负荷的 Azure 平台功能
* [Microsoft 安全响应中心](http://www.microsoft.com/security/msrc/default.aspx)-这里可以报告 Microsoft 安全漏洞，包括使用 Azure，问题，也可以通过电子邮件和[secure@microsoft.com](mailto:secure@microsoft.com)
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/)– 保持最新上最新的 Azure 安全

<!--Image references-->
[1]: ./media/azure-security-management/typical-management-network-topology.png
[2]: ./media/azure-security-management/stand-alone-hardened-workstation-topology.png
[3]: ./media/azure-security-management/hardened-workstation-enabled-with-hyper-v.png
[4]: ./media/azure-security-management/hardened-workstation-using-windows-to-go-on-a-usb-flash-drive.png
