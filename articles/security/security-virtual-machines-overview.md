---
title: "与 Azure 虚拟机配合使用的 Azure 安全功能 | Microsoft Docs"
description: " 能够与 Azure 虚拟机配合使用的 Azure 核心安全功能概述。 通过 Azure VM 可灵活进行虚拟化，无需购买和维护运行 VM 的物理硬件。 "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: f1fb7f876c7dc010c03f01a4f6698ddc18da1100
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---
# <a name="azure-virtual-machines-security-overview"></a>Azure 虚拟机安全概述
借助 Azure 虚拟机，可以采用灵活的方式部署各种计算解决方案。 通过对 Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP 和 Azure BizTalk 服务的支持，可以在几乎所有操作系统上部署任何工作负荷和任何语言。

Azure 虚拟机可用于灵活地进行虚拟化，而无需购买和维护运行虚拟机的物理硬件。  可以构建并部署应用程序，并且保证在我们的高度安全的数据中心数据受到保护而且是安全的。

借助 Azure，可以构建更加安全的、合规的解决方案：

* 保护虚拟机不受病毒和恶意软件的侵害
* 加密敏感数据
* 保护网络流量的安全
* 识别和检测威胁
* 满足合规性要求

本文旨在概述能与虚拟机配合使用的核心 Azure 安全功能。 我们还提供了一些文章链接，其中每篇文章都包含每个功能的详细信息，以便可以了解更多内容。  

本文中介绍的核心 Azure 虚拟机的安全功能：

* 反恶意软件
* 硬件安全模块
* 虚拟机磁盘加密
* 虚拟机备份
* Azure Site Recovery
* 虚拟网络
* 安全策略管理和报告
* 合规性

## <a name="antimalware"></a>反恶意软件
借助 Azure，可以使用来自 Microsoft、Symantec、Trend Micro、McAfee 和 Kaspersky 等安全性供应商的恶意软件清除软件，以保护虚拟机免受恶意文件、广告软件和其他威胁的侵害。 请参阅下面的“了解更多”部分以查找有关合作伙伴解决方案的文章。

适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件是一种实时保护功能，可帮助识别并删除病毒、间谍软件和其他恶意软件。  Microsoft 反恶意软件提供了已知恶意或不需要的软件试图安装自身或在 Azure 系统上运行时的可配置警报。

Microsoft 反恶意软件是一个针对应用程序和租户环境所提供的单一代理解决方案，可在后台运行而无需人工干预。 可以根据应用程序工作负荷的需求，选择默认的基本安全性或高级的自定义配置（包括反恶意软件监视）来部署保护。

部署并启用 Microsoft 反恶意软件后，便可以使用以下几项核心功能：

* 实时保护 - 监视云服务中和虚拟机上的活动，以检测和阻止恶意软件的执行。
* 计划的扫描 - 定期执行有针对性的扫描，以检测恶意软件，包括主动运行的程序。
* 恶意软件消除 - 自动针对检测到的恶意软件采取措施，例如删除或隔离恶意文件以及清除恶意注册表项。
* 签名更新 - 自动安装最新的保护签名（病毒定义）以确保按预定的频率保持最新保护状态。
* 反恶意软件引擎更新 - 自动更新 Microsoft 反恶意软件引擎。
* 反恶意软件平台更新 - 自动更新 Microsoft 反恶意软件平台。
* 主动保护 - 将检测到的威胁和可疑资源的遥测元数据报告给 Azure，以确保快速响应，并通过 Microsoft Active Protection System (MAPS) 启用实时同步签名传递。
* 示例报告 - 将示例提供并报告给 Microsoftt 反恶意软件服务，以帮助改善服务并实现故障排除。
* 排除项 - 允许应用程序和服务管理员配置特定的文件、进程与驱动器，以便出于性能和其他原因将其从保护和扫描中排除。
* 恶意软件事件收集 - 在操作系统事件日志中记录反恶意软件服务的运行状况、可疑活动及采取的补救措施，并将这些数据收集到客户的 Azure 存储帐户。

了解更多：若要详细了解反恶意软件以保护虚拟机，请参阅：

* [适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](azure-security-antimalware.md)
* [在 Azure 虚拟机上部署反恶意软件解决方案](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [如何在 Windows VM 上安装和配置 Trend Micro Deep Security 即服务](../virtual-machines/windows/classic/install-trend.md)
* [如何在 Windows VM 上安装和配置 Symantec Endpoint Protection](../virtual-machines/windows/classic/install-symantec.md)
* [Azure 应用商店中的安全解决方案](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>硬件安全模块
可通过提高密钥安全性来增强加密和身份验证保护。 通过将关键密码和密钥存储在 Azure 密钥保管库中，可以简化此类密码和密钥的管理和保护。 密钥保管库提供将密钥存储在已通过 FIPS 140-2 Level 2 标准认证的硬件安全模块 (HSM) 中的选项。 用于备份或[透明数据加密](https://msdn.microsoft.com/library/bb934049.aspx)的 SQL Server 加密密钥均可存储在密钥保管库中，此外还可存储应用程序中的任意密钥或密码。 对这些受保护项的权限和访问权限通过 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) 进行管理。

了解更多：

* [什么是 Azure 密钥保管库？](../key-vault/key-vault-whatis.md)
* [Azure 密钥保管库入门](../key-vault/key-vault-get-started.md)
* [Azure 密钥保管库博客](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>虚拟机磁盘加密
Azure 磁盘加密是用于加密 Windows 和 Linux Azure 虚拟机磁盘的新功能。 Azure 磁盘加密利用 Windows 的行业标准 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，为操作系统和数据磁盘提供卷加密。

该解决方案与 Azure Key Vault 集成，有助于控制和管理 Key Vault 订阅中的磁盘加密密钥和机密，同时确保虚拟机磁盘中的所有数据可在 Azure 存储中静态加密。

了解更多：

* [Azure Disk Encryption for Windows and Linux IaaS VMs](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)（适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密）
* [Azure Disk Encryption for Linux and Windows Virtual Machines](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)（适用于 Linux 和 Windows 虚拟机的 Azure 磁盘加密）
* [加密虚拟机](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>虚拟机备份
Azure 备份是一个可扩展的解决方案，无需资本投资便可保护应用程序数据，从而最大限度降低运营成本。 应用程序错误可能损坏数据，人为错误可能将 bug 引入应用程序。 借助 Azure 备份，可以保护运行 Windows 和 Linux 的虚拟机。

了解更多：

* [什么是 Azure 备份？](../backup/backup-introduction-to-azure-backup.md)
* [Azure 备份学习路径](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Azure 备份服务 - 常见问题解答](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
组织的 BCDR 策略的其中一个重要部分是，找出在发生计划的和非计划的中断时让企业工作负荷和应用保持启动并运行的方法。 Azure Site Recovery 可帮助协调工作负荷和应用的复制、故障转移及恢复，因此能够在主要位置发生故障时通过辅助位置来提供工作负荷和应用。

Site Recovery：

* **简化 BCDR 策略** — Site Recovery 可让你从单个位置轻松处理多个企业工作负荷和应用的复制、故障转移及恢复。 Site Recovery 会协调复制和故障转移，但不会拦截应用程序数据或拥有任何相关信息。
* **提供灵活的复制** — 借助 Site Recovery，可以复制 Hyper-V 虚拟机、VMware 虚拟机和 Windows/Linux 物理服务器上运行的工作负荷。
* **支持故障转移和恢复** — Site Recovery 提供测试故障转移，既能支持灾难恢复练习，又不会影响生产环境。 也可以运行计划的故障转移，因为是预期中的中断，所以不会丢失任何数据；或是运行非计划的故障转移，以在发生非预期的灾难时会数据损失减到最少（取决于复制频率）。 在故障转移之后，可以故障回复到主站点。 Site Recovery 提供的恢复计划可包含脚本和 Azure 自动化工作簿，可用于自定义多层应用程序的故障转移和恢复。
* 
            **消除辅助数据中心** — 可以复制到辅助本地站点或 Azure。 将 Azure 用作灾难恢复的目标可以消除维护辅助站点的复杂性和成本。 复制的数据存储在 Azure 存储中。
* **与现有 BCDR 技术集成** — Site Recovery 与其他应用程序 BCDR 功能。 例如，可以使用 Site Recovery 来保护公司工作负荷的 SQL Server 后端。 这包括对 SQL Server AlwaysOn 的本机支持以管理可用性组的故障转移。

了解更多：

* [什么是 Azure Site Recovery？](../site-recovery/site-recovery-overview.md)
* [Azure Site Recovery 的工作原理是什么？](../site-recovery/site-recovery-components.md)
* [哪些工作负荷受 Azure Site Recovery 保护？](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>虚拟网络
虚拟机需要网络连接。 要支持该要求，Azure 要求将虚拟机连接到 Azure 虚拟网络。 Azure 虚拟网络是一个构建于物理 Azure 网络结构之上的逻辑构造。 每个逻辑 Azure 虚拟网络都独立于所有其他 Azure 虚拟网络。 这种隔离可帮助确保部署中的网络流量对于其他 Microsoft Azure 客户不可访问。

了解更多：

* [Azure 网络安全概述](security-network-overview.md)
* [虚拟网络概述](../virtual-network/virtual-networks-overview.md)
* [Networking features and partnerships for Enterprise scenarios](https://azure.microsoft.com/blog/networking-enterprise/)（联网功能和企业方案的合作关系）

## <a name="security-policy-management-and-reporting"></a>安全策略管理和报告
Azure 安全中心帮助预防、检测和响应威胁，同时提高 Azure 资源的可见性并控制其安全性。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

Azure 安全中心通过以下方式来帮助优化和监视虚拟机安全：

* 提供虚拟机[安全建议](../security-center/security-center-recommendations.md)，如应用系统更新、配置 ACL 终结点、启用反恶意软件、启用网络安全组和应用磁盘加密。
* 监视虚拟机的状态

了解更多：

* [Azure 安全中心简介](../security-center/security-center-intro.md)
* [Azure 安全中心常见问题解答](../security-center/security-center-faq.md)
* [Azure 安全中心规划和操作](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>合规性
Azure 虚拟机已针对 FISMA、FedRAMP、HIPAA、PCI DSS Level 1 和其他关键合规性计划进行了认证。 此认证使自己的 Azure 应用程序更容易满足合规性要求，并使企业更容易应对各种国内和国际法规要求。

了解更多：

* [Microsoft Trust Center: Compliance](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)（Microsoft 信任中心：合规性）
* [Trusted Cloud: Microsoft Azure Security, Privacy, and Compliance](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)（可信云：Microsoft Azure 安全性、隐私和合规性）

