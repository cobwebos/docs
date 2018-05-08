---
title: Azure 中 IaaS 工作负荷的安全性最佳实践 | Microsoft Docs
description: " 将工作负荷迁移到 Azure IaaS 为重新评估设计带来了机会 "
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
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 2498c8b1acff79d50f34c703af77c555a00fd1a2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Azure 中 IaaS 工作负荷的安全性最佳实践

在开始考虑将工作负荷移到 Azure 基础结构即服务 (IaaS) 时，你可能会意识到一些注意事项似曾相识。 可能在保护虚拟环境方面有一定的经验。 迁移到 Azure IaaS 后，可以运用保护虚拟环境方面的专业知识，使用一组新的选项来帮助保护资产。

首先，我们不应期望将本地资源作为一一对应的资源加入到 Azure。 新的挑战和新的选项为重新评估现有设计、工具和进程提供了机会。

对安全的责任取决于云服务的类型。 下图总结了 Microsoft 和客户的责任平衡：


![责任范围](./media/azure-security-iaas/sec-cloudstack-new.png)


我们将讨论一些在 Azure 中可用的选项，这些选项有助于满足组织的安全要求。 请记住不同类型的工作负荷可能有不同的安全要求。 这些最佳实践没有一种可以单独保护系统。 就像安全中的其他内容，必须选择相应的选项，了解解决方案如何通过填补其他内容留下的缺口来相互补充。

## <a name="use-privileged-access-workstations"></a>使用特权访问工作站

组织通常成为网络攻击的牺牲品，原因在于管理员使用提升了权限的帐户执行操作。 虽然通常这并非出于恶意，只是因为现有的配置和进程允许他们执行此操作。 大多数用户从概念性角度了解这些操作的风险，但仍会选择执行这些操作。

查看电子邮件和浏览 Internet 等操作看似没有风险。 但它们可能会公开提升的帐户，从而受到恶意参与者的破坏。 浏览活动、特制电子邮件或其他技术可以用来访问企业。 强烈建议使用安全管理工作站 (SAW) 来执行所有 Azure 管理任务。 SAW 是减少意外泄露的一种方式。

特权访问工作站 (PAW) 为敏感任务提供专用操作系统，使其免受 Internet 攻击和威胁向量攻击。 将这些敏感的任务和帐户与日常使用的工作站及设备相分离，可提供有力的保护。 此分离操作可以限制网络钓鱼攻击、应用程序和 OS 漏洞、各种模拟攻击和凭证被盗攻击的影响。 （击键记录、哈希传递和票证传递）

PAW 方法是获得广泛认可的推荐做法的延伸，以使用单独分配的管理帐户。 管理帐户不同于标准用户帐户。 PAW 为这些敏感帐户提供可信的工作站。

有关详细信息和实现指南，请参阅[特权访问工作站](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations)。

## <a name="use-multi-factor-authentication"></a>使用多重身份验证

过去使用外围网络控制对公司数据的访问。 在云优先、移动优先的世界中，标识是控制平面：可以使用它从任何设备控制对 IaaS 服务的访问。 此外，还可以使用它获取数据使用位置和使用方式的可见性和见解。 保护 Azure 用户的数字身份是保护订阅免受身份盗用和其他犯罪攻击的基础。

为了保护帐户，可以采取的最有利的步骤之一是启用双重身份验证。 双重身份验证是一种不仅仅只使用密码进行身份验证的方法。 这有助于降低某人在成功得到其他人的密码后进行访问的风险。

[Azure 多重身份验证](../active-directory/authentication/multi-factor-authentication.md)可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。 它提供一系列简单的验证选项用于强身份验证 -- 电话、短信或移动应用通知。 用户可以选择偏好的方法。

使用多重身份验证的最简单方法是使用 Microsoft 验证器移动应用，该移动应用可在运行 Windows、iOS 和 Android 的移动设备上使用。 通过使用最新版本的 Windows 10 以及本地 Active Directory 与 Azure Active Directory (Azure AD) 的集成，可将 [Microsoft Hello 企业版](../active-directory/active-directory-azureadjoin-passport-deployment.md)用于无缝单一登录到 Azure 资源。 在这种情况下，Windows 10 设备用作身份验证的第二重因素。

对于管理 Azure 订阅的帐户以及可以登录到虚拟机的帐户，使用多重身份验证比仅使用密码提供更高级别的安全性。 使用其他形式的双重身份验证可能也可以，但如果它们尚未在生产环境中，部署可能比较复杂。

以下屏幕截图显示了一些可用于 Azure 多重身份验证的选项：

![多重身份验证选项](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>限制和约束管理访问

保护可以管理 Azure 订阅的帐户极其重要。 泄露任何这些帐户都会使可能采取的所有其他步骤的值无效，以确保数据的保密性和完整性。 如 [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) 最近所阐述的，内部攻击会为任何组织的整体安全性带来巨大威胁。

应该遵循以下类似条件对具有管理权限的人员进行评估：

- 他们是否执行了需要管理特权的任务？
- 任务执行的频率如何？
- 无法由另一个管理员来代表他们执行任务是否有具体的原因？

记录所有可替代授予特权的其他已知方法，以及每种方法不可接受的原因。

使用及时管理可在不需要这些权限的时间段内防止存在不必要的具有提升权限的帐户。 帐户在有限时间内具有提升的权限，管理员可以执行其作业。 然后在轮值时间结束时或任务完成后移除这些权限。

可以使用 [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) 来管理、监视和控制组织中的访问。 它有助于保持关注组织中个人执行的操作。 此外，通过引入合格管理的概念将适时管理引入 Azure AD。 这些个人具有可能被授予管理员权限的帐户。 这些类型的用户可以完成激活过程，并获得有限时间的管理员权限。


## <a name="use-devtest-labs"></a>使用开发测试实验室

在实验室和开发环境中使用 Azure，组织可通过移除由硬件采购引入的延迟问题来增加测试和开发中的的灵活度。 遗憾的是，对 Azure 不熟悉或期望帮助提高其采用率可能导致管理员对权限分配过于随意。 这种风险可能会无意间将组织暴露于内部攻击之下。 可能会授予一些用户更多的访问权限，超过了其应具有的访问权限。

[Azure 开发测试实验室](../devtest-lab/devtest-lab-overview.md)服务使用 [Azure 基于角色的访问控制](../role-based-access-control/overview.md) (RBAC)。 使用 RBAC 可将团队内的责任分到各角色，这些角色仅授予用户完成其工作所需的访问级别。 RBAC 附带了预定义角色（所有者、实验室用户和参与者）。 甚至可以使用这些角色将权限分配给外部合作伙伴，大幅简化协作。

由于开发测试实验室使用了 RBAC，因此可以创建其他[自定义角色](../lab-services/devtest-lab-grant-user-permissions-to-specific-lab-policies.md)。 开发测试实验室不仅简化了权限管理，还简化了设置预配过程。 此外，它还可以帮助处理开发和测试环境中团队的其他典型挑战。 它需要一些准备工作，但从长远来看，它将使团队处理事情变得更轻松。

Azure 开发测试实验室的功能包括：

- 管理控制用户可用的选项。 管理员可集中管理允许的 VM 大小、最大 VM 数和 VM 的启动和关闭时间等。
- 自动创建实验室环境。
- 成本跟踪。
- 简化临时协作工作的 VM 分发。
- 自助服务允许用户使用模板设置自己的实验室。
- 管理和限制使用。

![使用开发测试实验室创建实验室](./media/azure-security-iaas/devtestlabs.png)

没有与使用开发测试实验室相关的其他成本。 创建实验室、策略、模板和项目都是免费的。 只需为实验室中使用的 Azure 资源（例如虚拟机、存储帐户和虚拟网络）付费。



## <a name="control-and-limit-endpoint-access"></a>控制和限制终结点访问

在 Azure 中承载实验室或生产系统意味着可从 Internet 访问系统。 默认情况下，新的 Windows 虚拟机的 RDP 端口可从 Internet 访问，并且 Linux 虚拟机的 SSH 端口打开。 为了将未经授权访问的风险降至最低，有必要采取措施限制公开的终结点。

Azure 中的一些技术可有助于限制对这些管理终结点的访问。 在 Azure 中，可以使用[网络安全组](../virtual-network/virtual-networks-nsg.md) (NSG)。 在使用 Azure 资源管理器进行部署时，NSG 将限制从所有网络访问此管理终结点（RDP 或 SSH）。 考虑 NSG 时，请考虑路由器 ACL。 可以使用它们紧密控制 Azure 网络的各个部分之间的网络通信。 这类似于在外围网络或其他隔离的网络中创建网络。 它们不检查流量，但它们将有助于网络分段。


在 Azure 中，可以从本地网络配置[站点到站点 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。 站点到站点 VPN 将本地网络扩展到云。 这样就会提供另一个使用 NSG 的机会，因为还可以将 NSG 修改为不允许从除本地网络外的任何其他位置进行访问。 然后，可以要求首先通过 VPN 连接到 Azure 网络来进行此管理。

在托管在 Azure 中与本地资源紧密集成的生产系统的情况下，站点到站点 VPN 选项可能最具吸引力。

或者，在想要管理不需要访问本地资源的系统的情况下，可使用[点到站点](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)选项。 这些系统可在其自身的 Azure 虚拟网络中隔离 管理员可通过 VPN 从其管理工作站连接到 Azure 托管的环境。

>[!NOTE]
>可以使用任一 VPN 选项在 NSG 上重新配置 ACL，以便不允许从 Internet 访问管理终结点。

值得考虑的另一个选项是[远程桌面网关](../active-directory/authentication/howto-mfaserver-nps-rdg.md)部署。 在将更精细的控制应用到这些连接时，可以使用此部署通过 HTTPS 安全地连接到远程桌面服务器。

可以访问的功能包括：

- 限制连接到特定系统的请求的管理员选项。
- 智能卡身份验证或 Azure 多重身份验证。
- 控制可以通过网关连接的系统。
- 控制设备和磁盘重定向。

## <a name="use-a-key-management-solution"></a>使用密钥管理解决方案

安全的密钥管理对在云中保护数据必不可少。 借助 [Azure 密钥保管库](../key-vault/key-vault-whatis.md)，可以在硬件安全模块 (HSM) 中安全存储加密密钥和小机密（例如密码）。 为了提升可靠性，可以在 HSM 中导入或生成密钥。

Microsoft 会在通过 FIPS 140-2 第 2 级验证的 HSM（硬件和固件）中处理密钥。 使用 Azure 日志记录来监视和审核密钥使用情况；将日志传输到 Azure 或安全信息与事件管理 (SIEM) 解决方案做进一步分析和威胁检测。

具有 Azure 订阅的任何人都可以创建和使用密钥保管库。 尽管 Key Vault 能够为开发人员和安全管理员提供便利，但负责管理 Azure 服务的组织管理员也可以实现和管理 Key Vault。


## <a name="encrypt-virtual-disks-and-disk-storage"></a>加密虚拟磁盘和磁盘存储

[Azure 磁盘加密](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)解决了因移动磁盘实现的未授权访问引起的数据盗用和泄密威胁。 可将磁盘附加到另一个系统作为一种绕过其他安全控制的方法。 磁盘加密在 Windows 中使用 [BitLocker](https://technet.microsoft.com/library/hh831713)，在 Linux 中使用 DM-Crypt 对操作系统和数据驱动器进行加密。 Azure 磁盘加密与 Key Vault 集成，可以控制和管理加密密钥。 它适用于标准 VM 和包含高级存储的 VM。

有关详细信息，请参阅 [Windows 和 Linux IaaS VM 中的 Azure 磁盘加密](azure-security-disk-encryption.md)。

[Azure 存储服务加密](../storage/common/storage-service-encryption.md)可帮助保护静态数据。 它在存储帐户级别启用。 它在向数据中心写入数据时对数据进行加密，并在访问数据时自动解密。 它支持以下方案：

- 块 Blob、追加 Blob 和页 Blob 的加密
- 从本地移到 Azure 的存档 VHD 和模板的加密
- 使用 VHD 创建的 IaaS VM 基础 OS 和数据磁盘的加密

在继续进行 Azure 存储加密之前，应注意两个限制：

- 它不可用于经典存储帐户。
- 它仅对启用加密后写入的数据进行加密。

## <a name="use-a-centralized-security-management-system"></a>使用集中的安全管理系统

需要监视服务器的修补、配置、事件和可能被视为安全问题的活动。 若要解决这些问题，可以使用[安全中心](https://azure.microsoft.com/services/security-center/)和 [Operations Management Suite 安全性与符合性](https://azure.microsoft.com/services/security-center/)。 这两个选项都胜过在操作系统中进行配置。 它们还提供对底层基础结构配置（例如网络配置和虚拟设备使用）的监视。

## <a name="manage-operating-systems"></a>管理操作系统

在 IaaS 部署中，仍负责管理在环境中像任何其他服务器或工作站一样部署的系统。 仍要负责修补、强化、权限分配以及与系统维护相关的任何其他活动。 对于与本地资源紧密集成的系统，建议对防病毒、反恶意软件、修补和备份等活动使用在本地使用的同一工具和过程。

### <a name="harden-systems"></a>强化系统
应强化 Azure IaaS 中的所有虚拟机，以便它们仅公开安装的应用程序所需的服务终结点。 对于 Windows 虚拟机，请遵循 Microsoft 发布作为[安全合规管理器](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx)解决方案基准的建议。

安全合规管理器是一个免费工具。 将与组策略和 System Center Configuration Manager 结合使用可以快速配置和管理桌面、传统数据中心、私有云和公有云。

安全合规管理器提供已经过测试的随时可部署的策略和 Desired Configuration Management 配置包。 这些基准基于 [Microsoft 安全指南](https://technet.microsoft.com/library/cc184906.aspx)建议和行业最佳做法。 它们可帮助你管理配置漂移、满足合规性要求，以及减少安全威胁。

可以使用安全合规管理器以两种不同的方法导入计算机的当前配置。 首先，可以导入基于 Active Directory 的组策略。 然后，可以通过使用 [LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) 工具导入“黄金母版”引用计算机的配置，以备份本地组策略。 稍后可将本地组策略导入安全合规管理器。

将标准与行业最佳做法相比较、对其进行自定义，并创建新的策略和 Desired Configuration Management 配置包。 已为所有受支持的操作系统（包括 Windows 10 周年更新和 Windows Server 2016）发布基准。


### <a name="install-and-manage-antimalware"></a>安装和管理反恶意软件

对于与生产环境分开托管的环境，可以使用反恶意软件扩展来帮助保护虚拟机和云服务。 该扩展与 [Azure 安全中心](../security-center/security-center-intro.md)集成。


[Microsoft 反恶意软件](azure-security-antimalware.md)包括实时保护、计划扫描、恶意软件修正、签名更新、引擎更新、示例报告、排除事件收集和 [PowerShell 支持](https://msdn.microsoft.com/library/dn771715.aspx)等功能。

![Azure 反恶意软件](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>安装最新的安全更新
客户移到 Azure 的部分首批工作负荷为实验室和面向外部的系统。 如果 Azure 托管的虚拟机托管需要访问 Internet 的应用程序或服务，则需要警惕修补。 修补不仅仅包括操作系统。 第三方应用程序上未修补的漏洞还可能导致一些问题，而如果实施良好的修补程序管理，就可以避免这些问题。

### <a name="deploy-and-test-a-backup-solution"></a>部署和测试备份解决方案

正如安全更新，需要以处理任何其他操作的同一方法处理备份。 这适合于属于扩展到云的生产环境的系统。 测试和开发系统必须遵循备份策略，这些策略可以根据用户的本地环境体验，提供与用户习惯的功能类似的存储功能。

如果可能，迁移到 Azure 的生产工作负荷应与现有的备份解决方案集成。 或者，可以使用 [Azure 备份](../backup/backup-azure-arm-vms.md)来帮助解决备份要求。


## <a name="monitor"></a>监视

[安全中心](../security-center/security-center-intro.md)持续评估 Azure 资源的安全状态，以找出潜在的安全漏洞。 会有一列建议对所需控件的整个配置过程提供指导。

示例包括：

- 预配反恶意软件可帮助识别和删除恶意软件。
- 配置网络安全组和规则来控制发送到虚拟机的流量。
- 预配 web 应用程序防火墙，帮助抵御针对 web 应用程序的攻击。
- 部署缺少的系统更新。
- 修正与建议的基线不匹配的 OS 配置。

下图显示了可在安全中心启用的某些选项。

![Azure 安全中心策略](./media/azure-security-iaas/security-center-policies.png)

[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) 是 Microsoft 的基于云的 IT 管理解决方案，可帮助管理和保护本地和云基础结构。 由于 Operations Management Suite 作为基于云的服务实现，因此在基础结构资源上进行极低的投资即可快速对其进行部署。

自动交付新增功能，从而节省持续维护和升级成本。 Operations Management Suite 还与 System Center Operations Manager 集成。 它具有不同的组件，可帮助你更好地管理 Azure 工作负荷，包括[安全性和符合性](../operations-management-suite/oms-security-getting-started.md)模块。

可以使用 Operations Management Suite 中的安全性和合规性功能查看有关资源的信息。 这些信息划分为四个主要类别：

- **安全域**：进一步浏览各时间段的安全记录。 访问恶意软件评估、更新评估、网络安全信息、标识和访问信息以及具有安全事件的计算机。 快速访问 Azure 安全中心仪表板。
- **值得注意的问题**：快速识别未解决的问题数和这些问题的严重性。
- **检测（预览版）**：当针对资源的安全警报出现时对其进行可视化，从而识别攻击模式。
- **威胁智能**：对出站恶意 IP 通信的服务器总数、恶意威胁类型和显示恶意 IP 来源的地图进行可视化，从而识别攻击模式。
- **常见安全查询**：查看可用于监视环境的最常见安全查询列表。 单击其中一个查询时，将打开“搜索”边栏选项卡并显示查询的结果。

以下屏幕截图显示了 Operations Management Suite 可显示的信息示例。

![Operations Management Suite 安全基准](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>后续步骤

* [Azure 安全团队博客](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft 安全响应中心](https://technet.microsoft.com/library/dn440717.aspx)
* [Azure 安全最佳做法和模式](security-best-practices-and-patterns.md)
