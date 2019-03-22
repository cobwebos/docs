---
title: 关于使用 Azure Site Recovery 进行 VMware VM 到 Azure 的灾难恢复 | Microsoft Docs
description: 本文概述了使用 Azure Site Recovery 服务执行从 VMware VM 到 Azure 的灾难恢复。
author: raynew
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: raynew
ms.openlocfilehash: 9d7b94500320315c1379ba3dfb8b6460bc105b49
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311192"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>关于 VMware VM 到 Azure 的灾难恢复

本文概述了使用 [Azure Site Recovery](site-recovery-overview.md) 服务执行从本地 VMware VM 到 Azure 的灾难恢复。

## <a name="what-is-bcdr"></a>什么是 BCDR？

业务连续性和灾难恢复 (BCDR) 策略有助于保持业务正常运行。 在计划的停机和意外故障期间，BCDR 可确保数据安全可用，并确保应用继续运行。 除了区域配对和高可用性存储等平台 BCDR 功能外，Azure 还提供恢复服务作为 BCDR 解决方案的一个主要部分。 恢复服务包括以下功能： 

- [Azure 备份](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)可备份用户的本地和 Azure VM 数据。 用户可以备份文件和文件夹、特定工作负载或整个 VM。 
- [Azure Site Recovery](site-recovery-overview.md) 为在本地计算机或 Azure IaaS VM 上运行的应用和工作负载提供恢复能力和灾难恢复。 Site Recovery 协调复制，并在发生中断时处理到 Azure 的故障转移。 它还处理从 Azure 到主站点的恢复。 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Site Recovery 如何进行灾难恢复？

1. 准备 Azure 和本地站点后，可以为本地计算机设置并启用复制。
2. Site Recovery 根据你的策略设置协调计算机的初始复制。
3. 完成初始复制后，Site Recovery 将增量更改复制到 Azure。 
4. 当所有内容按预期复制时，可以运行灾难恢复钻取。
    - 钻取有助于确保在真正需要时，故障转移将按预期工作。
    - 钻取执行测试故障转移时，不会影响你的生产环境。
5. 如果发生中断，则运行到 Azure 的完全故障转移。 可以对单台计算机进行故障转移，也可以创建一个恢复计划，同时在多台计算机上进行故障转移。
6. 故障转移时，从托管磁盘或存储帐户中的 VM 数据创建 Azure Vm。 用户可以继续从 Azure VM 访问应用和工作负载
7. 当本地站点再次可用时，便可从 Azure 进行故障回复。
8. 在进行故障回复并再次从主站点正常工作后，将再次开始将本地 VM 复制到 Azure。


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>我如何知道我的环境是否适合灾难恢复到 Azure？

Site Recovery 可复制受支持的 VMware VM 或物理服务器上运行的任何工作负载。 以下是需要在环境中检查的内容：

- 如果要复制 VMware VM，是否运行了正确版本的 VMware 虚拟化服务器？ [查看此文](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers)。
- 要复制的计算机是否运行支持的操作系统？ [查看此文](vmware-physical-azure-support-matrix.md#replicated-machines)。
- 对于 Linux 灾难恢复，计算机是否运行支持的文件系统/来宾存储？ [查看此文](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- 要复制的计算机是否符合 Azure 要求？ [查看此文](vmware-physical-azure-support-matrix.md#azure-vm-requirements)。
- 网络配置是否受支持？ [查看此文](vmware-physical-azure-support-matrix.md#network)。
- 存储配置是否受支持？ [查看此文](vmware-physical-azure-support-matrix.md#storage)。


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>在开始之前，我需要在 Azure 中设置什么？

在 Azure 中需要准备以下内容：

1. 验证 Azure 帐户是否具有在 Azure 中创建 VM 的权限。
2. 创建 Azure 虚拟机将加入时它们从创建存储帐户或托管的磁盘在故障转移后的 Azure 网络。
3. 为 Site Recovery 设置 Azure 恢复服务保管库。 保管库位于 Azure 门户中，用于部署、配置、协调、监视和排查 Site Recovery 部署的故障。

*需要更多帮助？*

了解如何设置 Azure 按[验证你的帐户](tutorial-prepare-azure.md#verify-account-permissions)，则创建[网络](tutorial-prepare-azure.md#set-up-an-azure-network)，并[设置保管库](tutorial-prepare-azure.md#create-a-recovery-services-vault)。



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>在开始之前，我需要在本地设置什么？

下面是需要在本地进行的设置：

1. 需要设置几个帐户：

    - 如果要复制 VMware VM，则 Site Recovery 需要一个帐户才能访问 vCenter Server 或 vSphere ESXi 主机以自动发现 VM。
    - 需要一个帐户才能在要复制的每台物理计算机或 VM 上安装 Site Recovery 移动服务代理。

2. 如果以前没有这样做，则需要检查 VMware 基础架构的兼容性。
3. 确保可以在故障转移后连接到 Azure VM。 在本地 Windows 计算机上设置 RDP，或在 Linux 计算机上设置 SSH。

*需要更多帮助？*
- 准备一个帐户用于[自动发现](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)和[安装移动服务](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)。
- [验证](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) VMware 设置是否都兼容。
- [准备](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover)就绪，以便在故障转移后在 Azure 中进行连接。
- 如果需要有关故障转移后为 Azure VM 设置 IP 寻址的更深入帮助，请[阅读本文](concepts-on-premises-to-azure-networking.md)。

## <a name="how-do-i-set-up-disaster-recovery"></a>如何设置灾难恢复？

在 Azure 和本地基础结构就位后，可以设置灾难恢复。

1. 若要了解需要部署的组件，请查看 [VMware 到 Azure 体系结构](vmware-azure-architecture.md)，以及[物理机到 Azure 体系结构](physical-azure-architecture.md)。 其中包含许多组件，请务必了解它们如何搭配在一起。
2. **源环境**：作为部署中的第一步，需要设置复制源环境。 指定要复制的内容以及要复制到的位置。
3. **配置服务器**：需要设置本地源环境中的配置服务器：
    - 配置服务器是一台本地计算机。 对于 VMware 灾难恢复，建议将其作为可通过可下载的 OVF 模板部署的 VMware VM 来部署。
    - 配置服务器协调本地环境与 Azure 之间的通信
    - 其他几个组件在配置服务器计算机上运行。
        - 进程服务器接收、 优化，并将复制数据发送到 Azure 中的缓存存储帐户。 它还会处理要复制的计算机上的移动服务的自动安装，并在 VMware 服务器上执行 VM 的自动发现。
        - 主目标服务器处理从 Azure 进行故障回复期间产生的复制数据。
    - 设置过程包括在保管库中注册配置服务器、下载 MySQL Server 和 VMware PowerCLI，以及指定为自动发现和移动服务安装所创建的帐户。
4. **目标环境**：您设置您的目标 Azure 环境通过指定你的 Azure 订阅和网络设置。
5. **复制策略**：指定复制的方式。 设置包括创建和存储恢复点的频率，以及是否应创建应用一致性快照。
6. **启用复制**。 为本地计算机启用复制。 如果你创建了一个帐户用于安装移动服务，则在为计算机启用复制时将进行安装。 

*需要更多帮助？*

- 有关这些步骤的快速演练，可以尝试学习我们的 [VMware 教程](vmware-azure-tutorial.md)，以及[物理服务器演练](physical-azure-disaster-recovery.md)。
- [详细了解](vmware-azure-set-up-source.md)如何设置源环境。
- [了解](vmware-azure-deploy-configuration-server.md)配置服务器要求，并使用 OVF 模板设置配置服务器以进行 VMware 复制。 如果出于某种原因无法使用模板，或者要复制物理服务器，请[使用这些说明](physical-azure-set-up-source.md#set-up-the-source-environment)。
- [详细了解](vmware-azure-set-up-target.md)目标设置。
- [详细了解](vmware-azure-set-up-replication.md)如何设置复制策略。
- [了解](vmware-azure-enable-replication.md)如何启用复制，并从复制中[排除](vmware-azure-exclude-disk.md)磁盘。


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>出现了问题，如何解决？

- 第一步，请尝试[监视部署](site-recovery-monitor-and-troubleshoot.md)，验证已复制项、作业和基础结构问题的状态，并确定所有错误。
- 如果无法完成初始复制，或者正在进行的复制未按预期工作，请[查看本文](vmware-azure-troubleshoot-replication.md)，了解常见错误和故障排查提示。
- 如果在要复制的计算机上自动安装移动服务时遇到问题，请查看[本文](vmware-azure-troubleshoot-push-install.md)中的常见错误。
- 如果故障转移未按预期工作，请查看[本文](site-recovery-failover-to-azure-troubleshoot.md)中的常见错误。
- 如果故障恢复无法正常运行，请检查问题是否出现在[本文](vmware-azure-troubleshoot-failback-reprotect.md)中。



## <a name="next-steps"></a>后续步骤

现在复制已准备就绪，应[运行灾难恢复钻取](tutorial-dr-drill-azure.md)以确保故障转移按预期工作。 
