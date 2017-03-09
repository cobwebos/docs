---
title: "Azure 中 IaaS 工作负荷的安全性最佳实践 | Microsoft Docs"
description: " 通过将工作负荷迁移到 Azure IaaS ，可重新评估我们的设计s "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2017
ms.author: barclayn
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 774feff39bee6f34b1fb292f130d8240ec070c81
ms.lasthandoff: 03/07/2017




---

# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Azure 中 IaaS 工作负荷的安全性最佳实践

在开始考虑将工作负荷移到 Azure IaaS 时，你可能会意识到一些注意事项似曾相识。 你可能有过保护虚拟环境方面的体验。 迁移到 Azure IaaS 后，你可以应用保护虚拟环境方面的专业知识，它还提供了一组新的选项来帮助保护资产。

开始之前，首先应明白不应期望将本地资源作为一一对应的资源加入到 Azure。 新的挑战和新的选项为重新评估现有设计、工具和进程提供了机会。




![责任范围](./media/azure-security-iaas/sec-cloudstack-new.png)

>[!NOTE]
> 你对安全的责任取决于云服务的类型。 上图总结了 Microsoft 和你的责任平衡。

## <a name="best-practices"></a>最佳实践
我们将讨论一些在 Azure 中可用的选项，这些选项有助于满足你组织的安全要求。 执行此操作时，必须记住不同类型的工作负荷，以及其安全要求的不同之处。 这些最佳实践没有一种可以单独保护你的系统。 就像安全中的其他内容，必须选择相应的选项，了解解决方案如何通过填补其他内容留下的缺口来相互补充。

### <a name="use-privileged-access-workstations-paw"></a>使用特权访问工作站 (PAW)

组织通常成为网络攻击的牺牲品，原因在于管理员使用提升了权限的帐户执行操作。 虽然通常这并非出于恶意，只是因为现有的配置和进程允许他们执行此操作。 大多数用户从概念性角度了解风险，但仍会选择他们认为危险的步骤。

查看电子邮件和浏览 Internet 等操作看似没有危险，但恶意的参与者会使用浏览活动（特别是特制电子邮件）或其他技术获得企业的访问权限，造成提升权限的帐户泄露。 强烈建议使用安全管理工作站执行所有 Azure 管理任务，以此减少意外泄露。

特权访问工作站 (PAW) 为敏感任务提供专用操作系统，使其免受 Internet 攻击和威胁向量攻击。 从日常使用的工作站和设备中分离这些敏感任务和帐户可提供强大的保护，使其免受网络钓鱼攻击、应用程序和操作系统漏洞攻击、各种模拟攻击和凭据盗窃攻击（例如按键日志记录、哈希传递和票证传递）。

PAW 方法分开使用管理人员的管理员帐户和用户帐户，是一种获得广泛认可的推荐做法。 这种做法使用单独分配的管理帐户，将其与用户的标准用户帐户相分离。 PAW 通过为这些敏感帐户提供可信工作站，建立此帐户分离做法。

有关特权访问工作站和 PAW 实现指南的详细信息，请点击此链接访问：

- [特权访问工作站](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations)

### <a name="use-multifactor-authentication"></a>使用多重身份验证

过去使用外围网络控制对公司数据的访问。 而当前确是云优先和移动优先，身份标识成为控制平面：用于从任何设备控制对 IaaS 服务的访问，以及获取数据使用位置和使用方式的可见性和见解。 保护 Azure 用户的数字身份是保护订阅免受身份盗用和其他犯罪攻击的基础。

为了保护帐户，可以采取的最有利的步骤之一是启用双重身份验证。 双重身份验证是一种不仅仅只使用密码进行身份验证的方法。 第二重因素为除了密码之外的内容。  这有助于降低某人在成功得到其他人的密码后进行访问的风险。

Azure Multi-Factor Authentication 可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。 多因素身份验证通过一系列简单的验证选项提供强身份验证，这些选项包括电话、短信或移动应用通知，用户可以根据自己的偏好选择所用的方法。

使用 [Azure MFA](../multi-factor-authentication/multi-factor-authentication.md) 的最简单方法是使用 Microsoft Authenticator 移动应用，该移动应用可在运行 Windows、IOS 和 Android 的移动设备上使用。 通过使用最新版本的 Windows 10 以及与 Azure AD 集成的本地 Active Directory，可将 [Microsoft Hello 企业版](../active-directory/active-directory-azureadjoin-passport-deployment.md)用于无缝单一登录到 Azure 资源。 在这种情况下，Windows 10 设备将用作身份验证的第二重因素。


就 Azure 而言，启用双重身份验证的最简单方法是使用 Azure 多重身份验证 (MFA)。 [Azure MFA](../multi-factor-authentication/multi-factor-authentication.md) 具有可用于移动设备的应用程序，该应用程序可通过电话呼叫、短信或在应用中生成的代码来工作，并且可以与本地目录集成。

对于管理 Azure 订阅的帐户，应使用 MFA，对于可以登录到虚拟机的帐户，在可能的情况下应使用 MFA。 对这些帐户使用 MFA 可比仅使用密码提供更高级别的安全性。 使用其他形式的双重身份验证也可以，但如果它们尚未在生产环境中，可能会需要更多部署。

以下屏幕截图显示了一些可用于 Azure MFA 身份验证的选项。

![MFA 选项](./media/azure-security-iaas/mfa-options.png)

### <a name="limit-and-constrain-administrative-access"></a>限制和约束管理访问

保护可以管理 Azure 订阅的帐户极其重要。 泄露任何这些帐户实际上都会使可能采取的所有其他步骤的值无效，以确保数据的保密性和完整性。 如 [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) 最近所述，机密信息的泄漏、内部攻击会为任何组织的整体安全性带来巨大威胁。

应按照以下类似条件对具有管理权限的人员进行评估：

- 他们是否执行了需要管理特权的任务？
- 任务执行的频率如何？
- 无法由另一个管理员来代表他们执行任务的具体原因。
- 记录所有可替代授予特权的其他已知方法，以及每种方法不可接受的原因。

使用及时管理可在不需要这些权限的时间段内防止存在不必要的具有提升权限的帐户。 帐户在有限时间内具有提升的权限，管理员可以执行作业，然后在轮值时间结束时或任务完成后移除这些权限。

[PIM](../active-directory/active-directory-privileged-identity-management-configure.md) 允许管理、监视和控制组织中的访问。 它有助于保持关注组织内个人执行的操作，并通过引入合格管理的概念将及时管理引入到 Azure AD 中。 这些个人具有可能被授予管理员权限的帐户。 这些类型的用户可以完成激活过程，并获得有限时间的管理员权限。


### <a name="use-devtest-labs"></a>使用开发测试实验室

在实验室和开发环境中使用 Azure，组织可通过移除由硬件采购引入的延迟问题来增加测试和开发中的的灵活度。 遗憾的是，对 Azure 不熟悉或期望帮助提高其采用率等风险可能导致管理员对权限分配过于随意。 这可能会无意间将组织暴露于内部攻击之下。 可能会授予一些用户更多的访问权限，超过了其应具有的访问权限。

Azure 中现在包含一个称为[开发测试实验室](../devtest-lab/devtest-lab-overview.md)的服务。 开发测试实验室使用[基于 Azure 角色的访问控制 (RBAC)](../active-directory/role-based-access-control-what-is.md)(RBAC)。 使用 RBAC，可将团队内的责任分到各角色，这些角色仅授予用户完成其工作所需的访问级别。 它附带了预定义角色（所有者、实验室用户和参与者）。 这些角色甚至可以用于将权限分配给外部合作伙伴，极大地简化了协作。

由于开发测试实验室使用了 RBAC，因此可以创建其他[自定义角色](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md)。 开发测试实验室不仅简化了权限管理，还旨在简化设置环境的过程，并处理开发和测试环境中团队的其他典型挑战。 它需要一些准备工作，但从长远来看，它将使团队处理事情变得更轻松。

一些关键 Azure 开发测试实验室功能包括：

- 管理控制用户可用的选项。 管理员可集中管理允许的 VM 大小、最大 VM 数和 VM 的启动和关闭时间等
- 自动化创建实验室环境
- 成本跟踪
- 简化临时协作工作的 VM 分发
- 自助服务允许用户使用模板设置自己的实验室
- 管理和限制使用

![创建开发测试实验室](./media/azure-security-iaas/devtestlabs.png)

没有与使用开发测试实验室相关的其他成本。 创建实验室、配置策略、模板和项目都是免费的。 仅支付在实验室中使用的 Azure 资源（例如虚拟机、存储帐户和虚拟网络）。



### <a name="control-and-limit-endpoint-access"></a>控制和限制终结点访问

在 Azure 中承载实验室或生产系统意味着可从 Internet 访问你的系统。 默认情况下，新的 Windows 虚拟机的 RDP 端口可从 Internet 访问，并且 Linux 虚拟机的 SSH 端口打开。 这意味着，为了将未经授权访问的风险降至最低，有必要采取措施“限制公开的终结点”。

Azure 中的一些技术可有助于限制对这些管理终结点的访问。 在 Azure 中，可以使用网络安全组 ([NSG](../virtual-network/virtual-networks-nsg.md))。 在使用 Resource Manager 进行部署时，将使用 NSG 来限制从所有网络访问此管理终结点（RDP 或 SSH）。  考虑 NSG 时，请考虑路由器 ACL。 可以使用它们紧密控制 Azure 网络的各个部分之间的网络通信。 这类似于在 DMZ 或其他隔离的网络中创建网络。 它们不检查流量，但它们将有助于网络分段。


在 Azure 中，可以从本地网络配置[站点到站点的 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)，有效地将本地网络扩展到云。  这可提供另一个使用 NSG 的机会，因为还可以将 NSG 修改为不允许从除本地网络外的任何其他位置进行访问，然后要求首先通过 VPN 连接到 Azure 网络来进行此管理。

在托管在 Azure 中与本地资源紧密集成的生产系统的情况下，站点到站点 VPN 选项可能最具吸引力。

或者，在想要管理不需要访问本地资源的系统的情况下，可使用[点到站点](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)选项。 这些系统可在其自身的 Azure 虚拟网络中分离，并且管理员可通过 VPN 从其管理工作站连接到 Azure 托管的环境。

>[!NOTE]
任一 VPN 选项都可允许在 NSG 上重新配置 ACL，以便不允许从 Internet 访问管理终结点。

值得考虑的另一个选项是[远程桌面网关](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md)部署。 在将更精细的控制应用到这些连接时，可以使用此远程桌面网关部署通过 HTTPS 安全地连接到远程桌面服务器。

可以访问的某些功能包括：

- 限制连接到特定系统的请求的管理员选项。
- 智能卡身份验证或 Azure MFA
- 控制可以通过网关连接的系统。
- 控制设备和磁盘重定向。

### <a name="use-a-key-management-solution"></a>使用密钥管理解决方案

安全的密钥管理对在云中保护数据必不可少。 借助 [Azure 密钥保管库](../key-vault/key-vault-whatis.md)，可以在硬件安全模块 (HSM) 中安全存储加密密钥和小机密（例如密码）。 为了提升可靠性，可以在 HSM 中导入或生成密钥。

如果选择这样做，Microsoft 将在通过 FIPS 140-2 第 2 级验证的 HSM（硬件和固件）中处理密钥。 通过 Azure 日志记录监视并审核密钥的使用情况 - 将日志传送到 Azure 应用或 SIEM 中以进行额外的分析和威胁检测。

具有 Azure 订阅的任何人都可以创建和使用密钥保管库。 尽管密钥保管库能够为开发人员和安全管理员提供便利，但负责管理 Azure 服务的组织管理员也可以实现和管理密钥保管库。


### <a name="encrypt-virtual-disks-and-disk-storage"></a>加密虚拟磁盘和磁盘存储

[Azure 磁盘加密](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)解决了因移动磁盘实现的未授权访问引起的数据盗用和泄密威胁。 可将磁盘附加到另一个系统作为一种绕过其他安全控制的方法。 磁盘加密在 Windows 中使用 [BitLocker](https://technet.microsoft.com/library/hh831713 )，在 Linux 中使用 DM-Crypt 对操作系统和数据驱动器进行加密。 Azure 磁盘加密与密钥保管库集成，用于控制和管理加密密钥，它可用于标准 VM 和包含高级存储的 VM。

有关详细信息，请查看包含 [Windows 和 Linux IaaS VM 中的 Azure 磁盘加密](azure-security-disk-encryption.md)的文章。

[Azure 存储服务加密](../storage/storage-service-encryption.md)可保护静态数据。 Azure 存储服务加密在存储帐户级别启用，在向数据中心写入数据时对数据进行加密，并在你访问数据时自动解密。 它支持以下方案：

- 块 Blob、追加 Blob 和页 Blob 的加密。
- 从本地移到 Azure 的存档 VHD 和模板的加密。
- 使用 VHD 创建的 IaaS VM 基础 OS 和数据磁盘的加密。

在继续进行 Azure 存储加密之前，应注意两个值得注意的限制：

- 它不可用于经典存储帐户。
- 它仅对启用加密后写入的数据进行加密。

### <a name="use-a-centralized-security-management-system"></a>使用集中的安全管理系统

需要监视服务器的修补、配置、事件和可能被视为安全问题的活动。 若要解决这些问题，可以使用[安全中心](https://azure.microsoft.com/services/security-center/)和 [Operations Management Suite 安全性和合规性](https://azure.microsoft.com/services/security-center/)。 这两种选项都胜过在操作系统内进行配置，并且还提供对底层基础结构配置（例如网络配置和虚拟设备使用）的监视。

### <a name="operating-system-management-best-practices"></a>操作系统管理最佳做法

在 IaaS 部署中，你仍负责管理在环境中像任何其他服务器或工作站一样部署的系统。 这意味着，你仍要负责修补、强化、权限分配以及与系统维护相关的任何其他活动。  对于与本地资源紧密集成的系统，建议对防病毒、反恶意软件、修补和备份等活动使用在本地使用的同一工具和过程。

**强化**应强化 Azure IaaS 中的所有虚拟机，以便它们仅公开安装的应用程序所需的服务终结点。 对于 Windows 虚拟机，请遵循 Microsoft 发布作为安全合规管理器解决方案基准的建议。

[安全合规管理器](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) - 我们最近发布了版本 4.0 - 这是一个免费工具，允许使用组策略和 System Center Configuration Manager 快速配置和管理你的桌面、传统数据中心、私有云和公有云。

SCM 准备部署策略和并提供进行测试的 DCM 配置包。 这些基准基于 [Microsoft 安全指南](https://technet.microsoft.com/en-us/library/cc184906.aspx)建议和行业最佳做法，让你能够管理配置漂移、满足合规性要求，以及减少安全威胁。

可以利用 SCM 使用两种不同方法导入计算机的当前配置：首先，可以导入基于 Active Directory 的组策略；然后，可以通过使用 [LocalGPO 工具](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/)导入“黄金母版”引用计算机的配置，以便备份可以稍后导入到 SCM 的本地组策略。

将你的标准与行业最佳做法相比较、对其进行自定义，并创建新的策略和 DCM 配置包。
已为所有受支持的操作系统（包括 Windows 10 周年更新和 Windows Server 2016）发布基准。


**安装和管理反恶意软件**

对于与生产环境分开托管的环境，可以使用反恶意软件扩展来保护虚拟机和云服务，该反恶意软件扩展与 [Azure 安全中心](../security-center/security-center-intro.md)集成。


[Microsoft 反恶意软件](azure-security-antimalware.md)包括实时保护、计划扫描、恶意软件修正、签名更新、引擎更新、示例报告、排除事件收集和 [PowerShell 支持](https://msdn.microsoft.com/library/dn771715.aspx)等功能。

![Azure 反恶意软件](./media/azure-security-iaas/azantimalware.png)

**安装最新的安全更新**客户移到 Azure 的部分首批工作负荷为实验室和面向外部的系统。 如果在 Azure 中托管的虚拟机托管需要访问 Internet 的应用程序或服务，则需要警惕修补。 请记住，这要胜过修补操作系统。 第三方应用程序上未修补的漏洞还可能导致一些问题，而如果实施良好的修补程序管理，就可以轻松避免这些问题。

**部署和测试备份解决方案**

正如安全更新，需要以处理任何其他操作的同一方法处理备份。 这适合于属于扩展到云的生产环境的系统。 测试和开发系统必须遵循备份策略，该策略可以根据用户的本地环境体验，提供与用户习惯的功能类似的存储功能。

如果可能，移动到 Azure 的生产工作负荷应与现有的备份解决方案集成，或者可以使用 [Azure 备份](../backup/backup-azure-arm-vms.md)来帮助解决备份要求。


### <a name="monitor"></a>监视

[安全中心](../security-center/security-center-intro.md)持续评估 Azure 资源的安全状态，以找出潜在的安全漏洞。 会有一列建议对所需控件的整个配置过程提供指导。

示例包括：

- 设置反恶意软件可帮助识别和删除恶意软件
- 配置网络安全组和规则来控制发送到虚拟机的流量
- 设置 web 应用程序防火墙，帮助抵御针对 web 应用程序的攻击
- 部署缺少的系统更新
- 解决与推荐基线不匹配的操作系统配置

在下图中可以看到某些可在安全中心启用的选项。

![Azure 安全中心策略](./media/azure-security-iaas/security-center-policies.png)

 [Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) 是 Microsoft 的基于云的 IT 管理解决方案，可帮助管理和保护本地和云基础结构。 由于 OMS 作为基于云的服务实现，因此在基础结构资源上进行极低的投资即可快速对其进行部署。

自动交付新增功能，从而节省持续维护和升级成本。 它还与 System Center Operations Manager 集成。  OMS 具有不同的组件帮助你更好地管理 Azure 工作负荷，包括[安全性和合规性](../operations-management-suite/oms-security-getting-started.md)模块。

OMS 中的安全性和合规性功能允许你查看你的资源信息，这些信息被分为四个主要类别：

- 安全域：在此区域中可进一步了解：随时间推移的安全记录、访问恶意软件评估、更新评估、网络安全、身份和访问信息、具有安全事件的计算机，并快速访问 Azure 安全中心仪表板。
- 值得注意的问题：此选项允许快速标识未解决的问题数和这些问题的严重性。
- 检测（预览版）：当针对资源的安全警报出现时对其进行可视化，从而确定攻击模式。
- 威胁智能：对出站恶意 IP 通信的服务器总数、恶意威胁类型和显示恶意 IP 来源的地图进行可视化，从而确定攻击模式。
- 常见安全查询：此选项提供用来监视环境的最常见安全查询的列表。 单击其中一个查询时，将打开“搜索”边栏选项卡及该查询的结果

以下屏幕截图显示了 OMS 可显示的信息类型的示例。

![OMS 安全基准](./media/azure-security-iaas/oms-security-baseline.png)



## <a name="next-steps"></a>后续步骤


* [Azure 安全团队博客](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft 安全响应中心](https://technet.microsoft.com/library/dn440717.aspx)
* [Azure 安全最佳实践和模式](security-best-practices-and-patterns.md)

