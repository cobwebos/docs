---
title: "Site Recovery 中到 Azure 的测试故障转移 | Microsoft Docs"
description: "Azure Site Recovery 可以协调虚拟机和物理服务器的复制、故障转移与恢复。 了解有关故障转移到 Azure 或辅助数据中心的信息。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/09/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: f430d81889ecf7f3210358556b99d7d88b0e7a76
ms.openlocfilehash: 8d848385863aeea43dbad3991e7776e399ac3994


---
# <a name="test--failover-to-azure-in-site-recovery"></a>Site Recovery 中到 Azure 的测试故障转移

本文提供有关针对使用 Azure 作为恢复站点、受 Site Recovery 保护的虚拟机和物理服务器执行测试故障转移或灾难恢复演练的信息与说明。 

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

运行测试故障可以在不丢失任何数据或造成停机的情况下验证复制策略或执行灾难恢复演练。 执行测试故障转移不会对进行中的复制或者生产环境造成任何影响。 可在虚拟机或[恢复计划](site-recovery-create-recovery-plans.md)上执行测试故障转移。 触发测试故障转移时，需要指定测试虚拟机要连接的网络。 触发测试故障转移后，可在“作业”页中跟踪进度。  


## <a name="supported-scenarios"></a>支持的方案
除[旧版 VMware 站点到 Azure](site-recovery-vmware-to-azure-classic-legacy.md) 之外的所有部署方案都支持测试故障转移。 如果虚拟机已故障转移到 Azure，也不支持测试故障转移。  


## <a name="run-a-test-failover"></a>运行测试故障转移
本过程描述如何对恢复计划运行测试故障转移。 或者，也可以在单个虚拟机上使用相应的选项运行测试故障转移。 

1. 选择“**恢复计划**” > “*recoveryplan_name*”。 单击“测试故障转移”。
1. 选择要故障转移到的“恢复点”。 可以使用以下选项之一：
    1.  **最新处理**：此选项将恢复计划的所有虚拟机故障转移到已由站点恢复服务处理的最新恢复点。 对虚拟机执行测试故障转移时，还会显示最新处理的恢复点的时间戳。 如果要针对恢复计划执行故障转移，可以转到单个虚拟机，并查看“最新恢复点”磁贴来获取此信息。 由于不会花费时间来处理未经处理的数据，此选项是 RTO（恢复时间目标）较低的故障转移选项。 
    1.    **最新应用一致**：此选项将恢复计划的所有虚拟机故障转移到已由站点恢复服务处理的最新应用程序一致的恢复点。 对虚拟机执行测试故障转移时，还会显示最新应用一致性恢复点的时间戳。 如果要针对恢复计划执行故障转移，可以转到单个虚拟机，并查看“最新恢复点”磁贴来获取此信息。 
    1.    **最新**：此选项首先处理已发送到站点恢复服务的所有数据，为每个虚拟机创建恢复点，然后再将其故障转移到该恢复点。 此选项提供最低 RPO（恢复点目标），因为故障转移后创建的虚拟机将具有触发故障转移时已复制到站点恢复服务的所有数据。 
    1.  **自定义**：如果正在对虚拟机执行测试故障转移，则可以使用此选项故障转移到特定恢复点。
1. 选择“Azure 虚拟网络”：提供将在其中创建测试虚拟机的 Azure 虚拟网络。 站点恢复尝试在名称相同的子网中创建测试虚拟机，并使用虚拟机的“计算与网络”设置中提供的同一 IP。 如果为测试故障转移提供的 Azure 虚拟网络中没有同名的子网，则会按字母顺序在第一个子网中创建测试虚拟机。 如果子网中没有相同 IP，则虚拟机将接收子网中可用的其他 IP 地址。 请阅读此部分以了解[详细信息](#creating-a-network-for-test-failover)
1. 如果要故障转移到 Azure 并且启用了数据加密，请在“加密密钥”中，选择在安装提供程序期间启用数据加密时颁发的证书。 如果尚未在虚拟机上启用加密，则可以忽略此步骤。
1. 在“**作业**”选项卡上跟踪故障转移进度。 在 Azure 门户中，你应当能够看到测试副本计算机。
1. 若要在虚拟机上启动 RDP 连接，需要在故障转移的虚拟机的网络接口上[添加公用 IP ](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine)。 如果要故障转移到经典虚拟机，则需要在端口 3389 上[添加终结点](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md)
1. 完成后，在恢复计划上单击“清理测试故障转移”。 在“**说明**”中，记录并保存与测试性故障转移相关联的任何观测结果。 此时会删除在测试故障转移期间创建的虚拟机。 


> [!TIP]
> 站点恢复尝试在名称相同的子网中创建测试虚拟机，并使用虚拟机的“计算与网络”设置中提供的同一 IP。 如果为测试故障转移提供的 Azure 虚拟网络中没有同名的子网，则会按字母顺序在第一个子网中创建测试虚拟机。 如果子网中没有相同 IP，则虚拟机将接收子网中可用的其他 IP 地址。 
>
> 


## <a name="creating-a-network-for-test-failover"></a>创建用于测试故障转移的网络 
执行测试故障转移时，建议选择与虚拟机的“计算和网络”设置中提供的生产恢复站点网络相隔离的网络。 默认情况下，创建 Azure 虚拟网络时，该网络独立于其他网络。 此网络应模拟生产网络：

1. 测试网络中的子网数目应与生产网络中的子网数目相同，并且其子网名称应与生产网络中子网的名称相同。
1. 测试网络使用的 IP 范围应与生产网络的 IP 范围相同。
1. 将测试网络的 DNS 的 IP 更新为在“计算和网络”设置下为 DNS 虚拟机指定的目标 IP。 如需更多详细信息，请参阅 [Active Directory 的测试性故障转移注意事项](site-recovery-active-directory.md#test-failover-considerations)部分。 


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>在恢复站点上执行到生产网络的测试故障转移 
执行测试故障转移时，建议选择与虚拟机的“计算和网络”设置中提供的生产恢复站点网络不同的网络。 但是，如果你确实想要在故障转移后的虚拟机中验证端到端网络连接，请注意以下几点：

1. 确保在执行测试故障转移时主虚拟机已关闭。 如果不这样做，则会有两个具有相同标识的虚拟机同时运行在同一网络中，并且可能导致意外后果。 
1. 清除测试故障转移虚拟机时，对测试故障转移虚拟机所做的任何更改都将丢失。 这些更改不会复制回主虚拟机。
1. 这种测试方式会导致生产应用程序停机。 灾难恢复演练正在进行时，应该告诉应用程序的用户不要使用该应用程序。  



## <a name="prepare-active-directory-and-dns"></a>准备 Active Directory 和 DNS
要运行测试故障转移以进行应用程序测试，测试中需要生产用 Active Directory 环境的副本。 如需更多详细信息，请参阅 [Active Directory 的测试性故障转移注意事项](site-recovery-active-directory.md#test-failover-considerations)部分。 

## <a name="next-steps"></a>后续步骤
成功尝试测试故障转移后，可以尝试执行[故障转移](site-recovery-failover.md)。




<!--HONumber=Jan17_HO4-->


