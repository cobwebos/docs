---
title: "Azure Site Recovery 中的测试故障转移（VMM 到 VMM）| Microsoft Docs"
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
ms.date: 06/05/2017
ms.author: pratshar
ms.openlocfilehash: afc4790d5714ce7145c8f4291a05acc2e9882a9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Site Recovery 中的测试故障转移（VMM 到 VMM）


本文提供有关针对受 Azure Site Recovery 保护的虚拟机 (VM) 和物理服务器执行测试故障转移或灾难恢复 (DR) 演练的信息与说明。 你将使用 System Center Virtual Machine Manager (VMM) 托管的本地站点作为恢复站点。

需要运行测试故障转移，在不丢失任何数据或造成停机的情况下，验证复制策略或执行灾难恢复演练。 测试故障转移不会对进行中的复制或生产环境造成任何影响。 可对虚拟机或[恢复计划](site-recovery-create-recovery-plans.md)运行测试故障转移。 触发测试故障转移时，需要指定测试虚拟机将连接的网络。 可在“作业”页面跟踪测试故障转移的进度。  

如有任何评论或问题，请发布到本文底部或 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="prepare-the-infrastructure-for-test-failover"></a>准备测试故障转移的基础结构
如果要使用现有网络运行测试故障转移，则应在该网络中准备 Active Directory、DHCP 和 DNS。

如果要通过利用选项自动创建 VM 网络来运行测试故障转移，请在要用于测试故障转移的恢复计划的第 1 组前添加手动步骤。 接下来将基础结构资源添加到自动创建的网络，然后运行测试故障转移。

### <a name="things-to-note"></a>需要注意的事项
复制到辅助站点时，副本计算机使用的网络类型不需要与用于测试故障转移的逻辑网络类型相匹配，但某些组合可能不起作用。 如果副本使用 DHCP 和基于 VLAN 的隔离，则副本的 VM 网络不需要静态 IP 地址池。 因此，无法为测试故障转移使用 Windows 网络虚拟化，因为没有任何可用的地址池。 

此外，如果副本网络使用“无隔离”并且测试网络使用“Windows 网络虚拟化”，则测试故障转移将不起作用。 这是因为，“无隔离”网络不提供创建 Windows 网络虚拟化网络所需的子网。

故障转移后，副本虚拟机连接到映射 VM 网络的方式取决于 VMM 控制台中 VM 网络的配置方式。

#### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>配置为无隔离或 VLAN 隔离的 VM 网络
如果为 VM 网络定义 DHCP，副本虚拟机将通过为关联逻辑网络中的网络站点指定的设置连接到 VLAN ID。 虚拟机将从可用的 DHCP 服务器接收其 IP 地址。 

不需要为目标 VM 网络定义静态 IP 地址池。 如果为 VM 网络使用静态 IP 地址池，副本虚拟机将通过为关联逻辑网络中的网络站点指定的设置连接到 VLAN ID。

虚拟机将从为 VM 网络定义的池接收其 IP 地址。 如果未在目标 VM 网络上定义静态 IP 地址池，则 IP 地址分配会失败。 应同时在将用于保护和恢复的源和目标 VMM 服务器上创建 IP 地址池。

#### <a name="vm-network-with-windows-network-virtualization"></a>使用 Windows 网络虚拟化的 VM 网络
如果使用 Windows 网络虚拟化配置 VM 网络，则应为目标 VM 网络定义静态池，无论源 VM 网络是配置为使用 DHCP 还是静态 IP 地址池。 

如果定义 DHCP，目标 VMM 服务器将充当 DHCP 服务器，并从为目标 VM 网络定义的池中提供一个 IP 地址。 如果为源服务器定义了使用静态 IP 地址池，则目标 VMM 服务器将从池中分配一个 IP 地址。 在这两种情况下，如果未定义静态 IP 地址池，则 IP 地址分配会失败。


### <a name="prepare-dhcp"></a>准备 DHCP
如果测试故障转移中涉及的虚拟机使用 DHCP，请在隔离网络中创建测试 DHCP 服务器以用于测试故障转移。

### <a name="prepare-active-directory"></a>准备 Active Directory
要运行测试故障转移以进行应用程序测试，测试环境中需要具有生产用 Active Directory 环境的副本。 有关详细信息，请参阅 [Active Directory 的测试故障转移注意事项](site-recovery-active-directory.md#test-failover-considerations)。

### <a name="prepare-dns"></a>准备 DNS
按如下所述准备 DNS 服务器以进行测试故障转移：

* DHCP：如果虚拟机使用 DHCP，则应在测试 DHCP 服务器上更新测试 DNS 的 IP 地址。 如果使用的网络类型为 Windows 网络虚拟化，则 VMM 服务器充当 DHCP 服务器。 因此，应在测试性故障转移网络中更新 DNS 的 IP 地址。 在这种情况下，虚拟机将向相关的 DNS 服务器注册其本身。
* 静态地址：如果虚拟机使用静态 IP 地址，则应在测试故障转移网络中更新测试 DNS 服务器的 IP 地址。 可能需要使用测试虚拟机的 IP 地址更新 DNS。 可以使用以下示例脚本实现此目的：

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>运行测试故障转移
本过程描述如何对恢复计划运行测试故障转移。 或者，也可以在“虚拟机”选项卡上对单个虚拟机运行故障转移。

![“测试故障转移”边栏选项卡](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. 选择“**恢复计划**” > “*recoveryplan_name*”。 单击“**故障转移**” > “**测试故障转移**”。
1. 在“测试故障转移”边栏选项卡上，指定在测试故障转移后，虚拟机应如何连接到网络。 有关详细信息，请参阅[网络选项](#network-options-in-site-recovery)。
1. 在“**作业**”选项卡上跟踪故障转移进度。
1. 故障转移完成后，验证虚拟机是否成功启动。
1. 完成后，在恢复计划上单击“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 此步骤会删除在测试故障转移期间创建的虚拟机和网络。


## <a name="network-options-in-site-recovery"></a>Site Recovery 中的网络选项

运行测试故障转移时，系统将要求为测试副本计算机选择网络设置。 有多个选项可供选择。  

| **测试故障转移选项** | **说明** | **故障转移检查** | **详细信息** |
| --- | --- | --- | --- |
| **故障转移到辅助 VMM 站点 - 无网络** |不要选择 VM 网络。 |故障转移会检查是否已创建测试计算机。<br/><br/>在副本虚拟机所在的主机上创建测试虚拟机。 它不会被添加到副本虚拟机所在的云中。 |<p>故障转移计算机不会连接到任何网络。<br/><br/>创建后，计算机可以连接到 VM 网络。 |
| **故障转移到辅助 VMM 站点 - 有网络** |选择现有 VM 网络。 |故障转移将检查是否已创建虚拟机。 |在副本虚拟机所在的主机上创建测试虚拟机。 它不会被添加到副本虚拟机所在的云中。<br/><br/>创建与生产网络隔离的 VM 网络。<br/><br/>如果使用的是基于 VLAN 的网络，我们建议在 VMM 中创建一个未在生产中使用的单独逻辑网络来实现此目的。 此逻辑网络用于创建 VM 网络以用于测试故障转移。<br/><br/>逻辑网络应至少与所有托管虚拟机的 Hyper-V 服务器中的一个网络适配器关联。<br/><br/>对于 VLAN 逻辑网络，应隔离在逻辑网络中添加的网络站点。<br/><br/>如果使用基于 Windows 网络虚拟化的逻辑网络，Azure Site Recovery 会自动创建隔离的 VM 网络。 |
| **故障转移到辅助 VMM 站点 - 创建网络** |将根据你在“逻辑网络”及其相关网站中指定的设置自动创建一个临时测试网络。 |故障转移将检查是否已创建虚拟机。 |如果恢复计划使用多个 VM 网络，则使用此选项。 如果使用 Windows 网络虚拟化网络，此选项可以使用副本虚拟机网络中的相同设置（子网和 IP 地址池）自动创建 VM 网络。 在测试故障转移完成后，会自动清理这些 VM 网络。</p><p>在副本虚拟机所在的主机上创建测试虚拟机。 它不会被添加到副本虚拟机所在的云中。 |

> [!TIP]
> 如果 IP 地址可用于测试故障转移网络，则在测试故障转移期间指定给虚拟机的 IP 地址与在计划或非计划故障转移时虚拟机收到的 IP 地址相同。 如果此相同的 IP 地址在测试故障转移网络中不可用，那么虚拟机会收到可在测试故障转移网络中使用的其他 IP 地址。
>
>


## <a name="test-failover-to-a-production-network-on-a-recovery-site"></a>在恢复站点上执行到生产网络的测试故障转移
执行测试故障转移时，建议选择与[网络映射](https://docs.microsoft.com/azure/site-recovery/site-recovery-network-mapping)中提供的生产恢复站点网络不同的网络。 但是，如果确实想要在故障转移后的虚拟机中验证端到端网络连接，请注意以下几点：

* 确保在执行测试故障转移时主虚拟机已关闭。 否则，具有相同标识的两个虚拟机将同时在同一网络中运行。 这种情况可能导致意外后果。
* 清理测试故障转移虚拟机时，对测试故障转移虚拟机所做的全部更改都会丢失。 这些更改不会复制回主虚拟机。
* 以这种方式执行测试会导致生产应用程序发生停机。 灾难恢复演练正在进行时，需告知应用程序的用户不要使用该应用程序。  


## <a name="next-steps"></a>后续步骤
成功运行测试故障转移后，可以尝试执行[故障转移](site-recovery-failover.md)。
