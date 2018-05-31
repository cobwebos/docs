---
title: 与 Azure 虚拟机配合使用的 Azure 安全功能 | Microsoft Docs
description: 本文概述了能够与 Azure 虚拟机配合使用的核心 Azure 安全功能。
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
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 7d31a434d4ead6dd8f8a13a08d368389904b5b4a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364227"
---
# <a name="azure-virtual-machines-security-overview"></a>Azure 虚拟机安全概述
可使用 Azure 虚拟机灵活地部署各种计算解决方案。 该服务支持 Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP 和 Azure BizTalk Services。 因此，几乎可在任何操作系统上部署任何工作负载和任何语言。

Azure 虚拟机可用于灵活地进行虚拟化，而无需购买和维护运行虚拟机的物理硬件。 可生成并部署应用程序，并保证数据在高度安全的数据中心受到保护且是安全的。

借助 Azure，可以构建更加安全的、合规的解决方案：

* 保护虚拟机不受病毒和恶意软件的侵害。
* 加密敏感数据。
* 保护网络流量的安全。
* 识别和检测威胁。
* 满足符合性要求。

本文旨在概述能与虚拟机配合使用的核心 Azure 安全功能。 此外还提供了一些文章链接，这些文章详细介绍了每个功能，可从中了解更多信息。  

## <a name="antimalware"></a>反恶意软件
通过 Azure，可使用安全供应商（例如 Microsoft、Symantec、Trend Micro 和 Kaspersky）提供的反恶意软件。 此软件可帮助保护虚拟机免受恶意文件、广告程序和其他威胁的侵害。

适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件是一种实时保护功能，可帮助识别并删除病毒、间谍软件和其他恶意软件。  适用于 Azure 的 Microsoft 反恶意软件提供可配置警报，能在已知恶意软件或不需要的软件试图自行安装或在 Azure 系统上运行时进行警报通知。

适用于 Azure 的 Microsoft 反恶意软件是针对应用程序和租户环境的单一代理解决方案。 它旨在后台运行，且无需人工干预。 可以根据应用程序工作负荷的需求，选择默认的基本安全性或高级的自定义配置（包括反恶意软件监视）来部署保护。

部署并启用适用于 Azure 的 Microsoft 反恶意软件后，可使用以下几项核心功能：

* **实时保护**：监视云服务中和虚拟机上的活动，检测并阻止恶意软件的执行。
* **计划的扫描**：定期执行有针对性的扫描，检测恶意软件（包括主动运行的程序）。
* **恶意软件消除**：自动针对检测到的恶意软件采取措施，例如删除或隔离恶意文件以及清除恶意注册表项。
* **签名更新**：自动安装最新的保护签名（病毒定义），确保按预定的频率保持最新保护状态。
* **反恶意软件引擎更新**：自动更新适用于 Azure 的 Microsoft 反恶意软件引擎。
* **反恶意软件平台更新**：自动更新适用于 Azure 的 Microsoft 反恶意软件平台。
* **主动保护**：将检测到的威胁和可疑资源的遥测元数据报告到 Azure，确保快速响应。 通过 Microsoft Active Protection System (MAPS) 启用实时同步签名发送。
* **示例报告**：将示例提供并报告给适用于 Azure 的 Microsoftt 反恶意软件服务，帮助改善服务并实现故障排除。
* **排除项**：允许应用程序和服务管理员配置特定的文件、进程与驱动器，以便出于性能和其他原因将其从保护和扫描中排除。
* **恶意软件事件收集**：在操作系统事件日志中记录反恶意软件服务的运行状况、可疑活动及采取的补救措施，并将这些数据收集到客户的 Azure 存储帐户。

了解有关反恶意软件的详细信息以保护虚拟机：

* [适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](azure-security-antimalware.md)
* [在 Azure 虚拟机上部署反恶意软件解决方案](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [如何在 Windows VM 上安装和配置服务型 Trend Micro Deep Security](../virtual-machines/windows/classic/install-trend.md)
* [如何在 Windows VM 上安装和配置 Symantec Endpoint Protection](../virtual-machines/windows/classic/install-symantec.md)
* [Azure Marketplace 中的安全解决方案](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>硬件安全模块
提高密钥安全性可增强加密和身份验证保护。 通过将关键密码和密钥存储在 Azure 密钥保管库中，可以简化此类密码和密钥的管理和保护。 

密钥保管库提供将密钥存储在已通过 FIPS 140-2 Level 2 标准认证的硬件安全模块 (HSM) 中的选项。 用于备份或[透明数据加密](https://msdn.microsoft.com/library/bb934049.aspx)的 SQL Server 加密密钥均可存储在密钥保管库中，此外还可存储应用程序中的任意密钥或密码。 对这些受保护项的权限和访问权限通过 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) 进行管理。

了解更多：

* [什么是 Azure 密钥保管库？](../key-vault/key-vault-whatis.md)
* [Azure 密钥保管库入门](../key-vault/key-vault-get-started.md)
* [Azure 密钥保管库博客](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>虚拟机磁盘加密
Azure 磁盘加密是用于加密 Windows 和 Linux 虚拟机磁盘的新功能。 Azure 磁盘加密利用 Windows 的行业标准 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，为 OS 和数据磁盘提供卷加密。

该解决方案与 Azure Key Vault 集成，帮助用户控制和管理 Key Vault 订阅中的磁盘加密密钥和机密。 它可确保虚拟机磁盘上的所有数据在 Azure 存储中静态加密。

了解更多：

* [Azure Disk Encryption for Windows and Linux IaaS VMs](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)（适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密）
* [Azure Disk Encryption for Linux and Windows Virtual Machines](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)（适用于 Linux 和 Windows 虚拟机的 Azure 磁盘加密）
* [加密虚拟机](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>虚拟机备份
Azure 备份是一种可缩放的解决方案，无需资本投资便可帮助保护应用程序数据，从而最大限度降低运营成本。 应用程序错误可能损坏数据，人为错误可能将 bug 引入应用程序。 借助 Azure 备份，可以保护运行 Windows 和 Linux 的虚拟机。

了解更多：

* [什么是 Azure 备份？](../backup/backup-introduction-to-azure-backup.md)
* [Azure 备份学习路径](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Azure 备份服务 - 常见问题解答](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
组织的 BCDR 策略的其中一个重要部分是，找出在发生计划的和非计划的中断时让企业工作负荷和应用保持运行的方法。 Azure Site Recovery 可帮助协调工作负荷和应用的复制、故障转移及恢复，因此能够在主要位置发生故障时通过辅助位置来提供工作负荷和应用。

Site Recovery：

* **简化 BCDR 策略**：通过 Site Recovery 可从一个位置轻松处理多个业务工作负荷和应用的复制、故障转移及恢复。 Site Recovery 会协调复制和故障转移，但不会拦截应用程序数据或拥有任何相关信息。
* **提供灵活的复制**：借助 Site Recovery，可以复制 Hyper-V 虚拟机、VMware 虚拟机和 Windows/Linux 物理服务器上运行的工作负荷。
* **支持故障转移和恢复**：Site Recovery 提供测试故障转移，既能支持灾难恢复练习，又不会影响生产环境。 也可以运行计划的故障转移，因为是预期中的中断，所以不会丢失任何数据；或是运行非计划的故障转移，以在发生非预期的灾难时会数据损失减到最少（取决于复制频率）。 故障转移之后，可故障回复到主站点。 Site Recovery 提供的恢复计划可包含脚本和 Azure 自动化工作簿，可用于自定义多层应用程序的故障转移和恢复。
* **消除辅助数据中心**：可复制到辅助本地站点或 Azure。 将 Azure 用作灾难恢复的目标可以消除维护辅助站点的复杂性和成本。 复制的数据存储在 Azure 存储中。
* **与现有 BCDR 技术集成**：Site Recovery 可与其他应用程序的 BCDR 功能配合使用。 例如，可使用 Site Recovery 来帮助保护公司工作负荷的 SQL Server 后端。 这包括对 SQL Server AlwaysOn 的本机支持以管理可用性组的故障转移。

了解更多：

* [什么是 Azure Site Recovery？](../site-recovery/site-recovery-overview.md)
* [Azure Site Recovery 的工作原理是什么？](../site-recovery/site-recovery-components.md)
* [哪些工作负荷受 Azure Site Recovery 保护？](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>虚拟网络
虚拟机需要网络连接。 若要支持该需求，Azure 要求将虚拟机连接到 Azure 虚拟网络。 

Azure 虚拟网络是一个构建于物理 Azure 网络结构之上的逻辑构造。 每个逻辑 Azure 虚拟网络都独立于所有其他 Azure 虚拟网络。 这种隔离可帮助确保部署中的网络流量对于其他 Microsoft Azure 客户不可访问。

了解更多：

* [Azure 网络安全概述](security-network-overview.md)
* [虚拟网络概述](../virtual-network/virtual-networks-overview.md)
* [Networking features and partnerships for Enterprise scenarios](https://azure.microsoft.com/blog/networking-enterprise/)（网络功能和用于企业方案的合作关系）

## <a name="security-policy-management-and-reporting"></a>安全策略管理和报告
Azure 安全中心可帮助防范、检测和应对威胁。 通过安全中心可提高对 Azure 资源安全性的可见性和控制力度。 它为 Azure 订阅提供集成的安全监控和策略管理。 它有助于检测可能会被忽视的威胁，适用于各种安全解决方案生态系统。

安全中心通过以下方式帮助优化和监视虚拟机的安全：

* 为虚拟机提供[安全建议](../security-center/security-center-recommendations.md)。 示例建议包括：应用系统更新、配置 ACL 终结点、启用反恶意软件、启用网络安全组和应用磁盘加密。
* 监视虚拟机的状态。

了解更多：

* [Azure 安全中心简介](../security-center/security-center-intro.md)
* [Azure 安全中心常见问题解答](../security-center/security-center-faq.md)
* [Azure 安全中心规划和操作](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>合规性
Azure 虚拟机已针对 FISMA、FedRAMP、HIPAA、PCI DSS Level 1 和其他关键合规性计划进行了认证。 此认证使自己的 Azure 应用程序更容易满足合规性要求，并使企业更容易应对各种国内和国际法规要求。

了解更多：

* [Microsoft Trust Center: Compliance](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)（Microsoft 信任中心：合规性）
* [Trusted Cloud: Microsoft Azure Security, Privacy, and Compliance](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)（可信云：Microsoft Azure 安全性、隐私和合规性）
