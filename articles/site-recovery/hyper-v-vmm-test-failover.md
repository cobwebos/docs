---
title: 使用 Azure Site Recovery 运行将 Hyper-V VM 灾难恢复到辅助站点的演练 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 运行将 VMM 云中的 Hyper-V VM 灾难恢复到辅助数据中心的演练。
services: site-recovery
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: ponatara
ms.openlocfilehash: c389776f62db5fd04f67ef22822e21fd4aee368f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>运行将 Hyper-V VM 灾难恢复到辅助站点的演练


本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md)，执行将 System Center Virtual Machine Manager V(MM) 云中管理的 Hyper-V VM 灾难恢复 (DR) 到辅助本地站点的演练。

需要运行测试故障转移，在不丢失任何数据或造成停机的情况下，验证复制策略或执行灾难恢复演练。 测试故障转移不会对进行中的复制或生产环境造成任何影响。 

## <a name="how-do-test-failovers-work"></a>测试故障转移的工作原理

假设你要运行从主要站点到辅助站点的测试故障转移。 如果只想检查 VM 的故障转移情况，则可以直接运行测试故障转移，而无需在辅助站点上进行任何设置。 若要验证应用故障转移是否按预期进行，则需要在辅助位置设置网络和基础结构。
- 可以针对单个 VM 或[恢复计划](site-recovery-create-recovery-plans.md)运行测试故障转移。
- 可以不通过网络、使用现有网络或者使用自动创建的网络运行测试故障转移。 下表中提供了有关这些选项的更多详细信息。
    - 可以不使用网络运行测试故障转移。 如果只想检查 VM 是否能够故障转移，但无法验证任何网络配置，则此选项非常有用。
    - 使用现有网络运行故障转移。 我们建议不要使用生产网络。
    - 运行故障转移，让 Site Recovery 自动创建测试网络。 在此情况下，Site Recovery 将自动创建网络，并在测试故障转移完成时清理该网络。
- 需要选择用于测试故障转移的恢复点： 
    - **最新处理**：此选项将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
    - **最新的应用一致**：此选项将 VM 故障转移到由 Site Recovery 处理的最新应用程序一致恢复点。 
    - **最新**：此选项首先处理已发送到 Site Recovery 服务的所有数据，为每个 VM 创建恢复点，然后将其故障转移到该恢复点。 此选项提供最低的 RPO（恢复点目标），因为故障转移后创建的 VM 具有触发故障转移时复制到 Site Recovery 的所有数据。
    - **最新多 VM 已处理**：适用于包含一个或多个已启用多 VM 一致性的 VM 的恢复计划。 已启用该设置的 VM 会故障转移到最新的常用多 VM 一致恢复点。 其他 VM 故障转移到最新的已处理恢复点。
    - **最新多 VM 应用一致性**：此选项适用于包含一个或多个已启用多 VM 一致性的 VM 的恢复计划。 属于复制组的 VM 会故障转移到最新的常用多 VM 应用程序一致恢复点。 其他 VM 故障转移到其最新的应用程序一致恢复点。
    - **自定义**：使用此选项可将特定的 VM 故障转移到特定的恢复点。



## <a name="prepare-networking"></a>准备网络

运行测试故障转移时，系统将要求为测试副本计算机选择网络设置。请参阅表中的摘要。

**选项** | **详细信息** 
--- | --- 
**无** | 将在副本 VM 所在的主机上创建测试 VM。 该 VM 不会添加到云中，且不会连接到任何网络。<br/><br/> 创建 VM 后，可将它连接到 VM 网络。
**使用现有项** | 将在副本 VM 所在的主机上创建测试 VM。 该 VM 不会添加到云中。<br/><br/>创建与生产网络隔离的 VM 网络。<br/><br/>如果使用的是基于 VLAN 的网络，我们建议在 VMM 中创建一个未在生产中使用的单独逻辑网络来实现此目的。 此逻辑网络用于创建 VM 网络以用于测试故障转移。<br/><br/>逻辑网络应至少与所有托管虚拟机的 Hyper-V 服务器中的一个网络适配器关联。<br/><br/>对于 VLAN 逻辑网络，应隔离在逻辑网络中添加的网络站点。<br/><br/>如果使用基于 Windows 网络虚拟化的逻辑网络，Azure Site Recovery 会自动创建隔离的 VM 网络。 
**创建网络** | 将根据你在“逻辑网络”及其相关网站中指定的设置自动创建一个临时测试网络。<br/><br/> 故障转移会检查是否已创建 VM。 |如果恢复计划使用多个 VM 网络，则应使用此选项。<br/><br/> 如果使用 Windows 网络虚拟化网络，此选项可以使用副本虚拟机网络中的相同设置（子网和 IP 地址池）自动创建 VM 网络。 在测试故障转移完成后，会自动清理这些 VM 网络。<br/><br/> 将在副本虚拟机所在的主机上创建测试 VM。 该 VM 不会添加到云中。

### <a name="best-practices"></a>最佳实践

- 测试生产网络会导致生产工作负荷出现停机。 灾难恢复演练进行期间，要求用户不要使用相关应用。

- 测试网络无需与用于测试故障转移的 VMM 逻辑网络类型匹配。 但是，某些组合不起作用：

     - 如果副本使用 DHCP 和基于 VLAN 的隔离，则副本的 VM 网络不需要静态 IP 地址池。 因此，无法为测试故障转移使用 Windows 网络虚拟化，因为没有任何可用的地址池。 
        
     - 如果副本网络使用“无隔离”并且测试网络使用“Windows 网络虚拟化”，则测试故障转移不起作用。 这是因为，“无隔离”网络不提供创建 Windows 网络虚拟化网络所需的子网。
        
- 我们建议不要将所选的网络用于网络映射、测试故障转移。

- 故障转移后，副本虚拟机连接到映射 VM 网络的方式取决于 VMM 控制台中 VM 网络的配置方式。


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>配置为无隔离或 VLAN 隔离的 VM 网络

如果在 VMM 中配置了无隔离或 VLAN 隔离的 VM 网络，请注意以下事项：

- 如果为 VM 网络定义 DHCP，副本虚拟机将通过为关联逻辑网络中的网络站点指定的设置连接到 VLAN ID。 虚拟机将从可用的 DHCP 服务器接收其 IP 地址。
- 不需要为目标 VM 网络定义静态 IP 地址池。 如果为 VM 网络使用静态 IP 地址池，副本虚拟机将通过为关联逻辑网络中的网络站点指定的设置连接到 VLAN ID。
- 虚拟机将从为 VM 网络定义的池接收其 IP 地址。 如果未在目标 VM 网络上定义静态 IP 地址池，则 IP 地址分配会失败。 应同时在将用于保护和恢复的源和目标 VMM 服务器上创建 IP 地址池。

### <a name="vm-network-with-windows-network-virtualization"></a>使用 Windows 网络虚拟化的 VM 网络

如果在 VMM 中配置了具有 Windows 网络虚拟化的 VM 网络，请注意以下事项：

- 应为目标 VM 网络定义静态池，无论源 VM 网络是配置为使用 DHCP 还是静态 IP 地址池。 
- 如果定义 DHCP，目标 VMM 服务器将充当 DHCP 服务器，并从为目标 VM 网络定义的池中提供一个 IP 地址。
- 如果为源服务器定义了使用静态 IP 地址池，则目标 VMM 服务器将从池中分配一个 IP 地址。 在这两种情况下，如果未定义静态 IP 地址池，则 IP 地址分配会失败。



## <a name="prepare-the-infrastructure"></a>准备基础结构

如果只想检查 VM 是否能够故障转移，可以直接运行测试故障转移，而无需准备基础结构。 若要执行测试应用故障转移的完整灾难恢复演练，则需要在辅助站点中准备基础结构：

- 若要使用现有网络运行测试故障转移，则应在该网络中准备 Active Directory、DHCP 和 DNS。
- 若要使用自动创建 VM 网络的选项运行测试故障转移，则需要先将基础结构资源添加到自动创建的网络，然后再运行测试故障转移。 在恢复计划中用于测试故障转移的组 1 前面添加一个手动步骤可以简化此操作。 接下来将基础结构资源添加到自动创建的网络，然后运行测试故障转移。


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

1. 选择“**恢复计划**” > “*recoveryplan_name*”。 单击“**故障转移**” > “**测试故障转移**”。
2. 在“测试故障转移”边栏选项卡上，指定在测试故障转移后，副本 VM 应如何连接到网络。
3. 在“**作业**”选项卡上跟踪故障转移进度。
4. 故障转移完成后，验证 VM 是否成功启动。
5. 完成后，在恢复计划上单击“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 此步骤会删除在测试故障转移期间由 Site Recovery 创建的所有 VM 和网络。 

![测试故障转移](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> 如果 IP 地址可用于测试故障转移网络，则在测试故障转移期间指定给虚拟机的 IP 地址与在计划或非计划故障转移时虚拟机收到的 IP 地址相同。 如果此相同的 IP 地址在测试故障转移网络中不可用，那么虚拟机会收到可在测试故障转移网络中使用的其他 IP 地址。



### <a name="run-a-test-failover-to-a-production-network"></a>运行到生产网络的测试故障转移

我们建议不要针对网络映射期间指定的生产恢复站点网络运行测试故障转移。 但是，如果确实需要在故障转移后的 VM 中验证端到端网络连接，请注意以下几点：

* 确保在执行测试故障转移时主 VM 已关闭。 否则，具有相同标识的两个虚拟机将同时在同一网络中运行。 这种情况可能导致意外后果。
* 清理测试故障转移 VM 时，对测试故障转移虚拟机所做的全部更改都会丢失。 这些更改不会复制回到主 VM。
* 以这种方式执行测试会导致生产应用程序发生停机。 灾难恢复演练正在进行时，需告知应用程序的用户不要使用该应用程序。  


## <a name="next-steps"></a>后续步骤
成功运行灾难恢复演练后，可以[运行完整故障转移](site-recovery-failover.md)。



