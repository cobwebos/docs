---
title: "Azure Site Recovery 中到 Azure 的测试故障转移 | Microsoft Docs"
description: "了解使用 Azure Site Recovery 服务运行从本地到 Azure 的测试故障转移。"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/08/2018
ms.author: raynew
ms.openlocfilehash: 442e9df550bec750afe0eab6e94b7e8550015fdd
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="test-failover-to-azure-in-site-recovery"></a>Site Recovery 中到 Azure 的测试故障转移


本文介绍如何使用 Site Recovery 测试故障转移运行到 Azure 的灾难恢复演练。  

运行测试故障转移可以验证复制和灾难恢复策略，且不会丢失任何数据或造成停机。 测试故障转移不会对正在进行的复制或生产环境造成任何影响。 可在特定的虚拟机 (VM) 或者包含多个 VM 的[恢复计划](site-recovery-create-recovery-plans.md)中运行测试故障转移。 


## <a name="run-a-test-failover"></a>运行测试故障转移
本过程描述如何对恢复计划运行测试故障转移。 

![测试故障转移](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. 在 Azure 门户上的“Site Recovery”中，单击“恢复计划” > “*recoveryplan_name*” > “测试故障转移”。
2. 选择要故障转移到的“恢复点”。 可以使用以下选项之一：
    - **最新处理**：此选项将计划中的所有 VM 故障转移到由 Site Recovery 处理的最新恢复点。 若要查看特定 VM 的最新恢复点，请检查 VM 设置中的“最新恢复点”。 此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
    - **最新的应用一致**：此选项将计划中的所有 VM 故障转移到由 Site Recovery 处理的最新应用程序一致恢复点。 若要查看特定 VM 的最新恢复点，请检查 VM 设置中的“最新恢复点”。 
    - **最新**：此选项首先处理已发送到 Site Recovery 服务的所有数据，为每个 VM 创建恢复点，然后将其故障转移到该恢复点。 此选项提供最低的 RPO（恢复点目标），因为故障转移后创建的 VM 具有触发故障转移时复制到 Site Recovery 的所有数据。
    - **最新多 VM 已处理**：此选项适用于包含一个或多个已启用多 VM 一致性的 VM 的恢复计划。 已启用该设置的 VM 会故障转移到最新的常用多 VM 一致恢复点。 其他 VM 故障转移到最新的已处理恢复点。  
    - **最新多 VM 应用一致性**：此选项适用于包含一个或多个已启用多 VM 一致性的 VM 的恢复计划。 属于复制组的 VM 会故障转移到最新的常用多 VM 应用程序一致恢复点。 其他 VM 故障转移到其最新的应用程序一致恢复点。 
    - **自定义**：使用此选项可将特定的 VM 故障转移到特定的恢复点。
3. 选择要在其中创建测试 VM 的 Azure 虚拟网络。

    - Site Recovery 尝试在子网中创建测试 VM，该子网的名称与 IP 地址与 VM 的“计算和网络”设置中提供的值相同。
    - 如果用于测试故障转移的 Azure 虚拟网络中没有同名的子网，则会按字母顺序在第一个子网中创建测试 VM。
    - 如果该子网中没有相同的 IP 地址，则 VM 会接收该子网中的另一个可用 IP 地址。 [了解详细信息](#creating-a-network-for-test-failover)。
4. 如果要故障转移到 Azure 并且启用了数据加密，请在“加密密钥”中，选择在安装提供程序期间启用加密时颁发的证书。 如果未启用加密，则可以忽略此步骤。
5. 在“**作业**”选项卡上跟踪故障转移进度。在 Azure 门户中，应当能够看到测试副本计算机。
6. 若要通过 RDP 与 Azure VM 发起连接，需在故障转移的 VM 的网络接口上[添加公共 IP 地址](https://aka.ms/addpublicip)。 
7. 如果一切符合预期，请单击“清理测试故障转移”。 这会删除在执行测试故障转移期间创建的 VM。
8. 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 


![测试故障转移](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

触发测试故障转移时，会发生以下情况：

1. **先决条件**：运行先决条件检查，确保符合故障转移所需的所有条件。
2. **故障转移**：故障转移会处理并准备好数据，以便能够基于这些数据创建 Azure VM。
3. **最新**：如果选择了最新的恢复点，则会基于发送到服务的数据创建恢复点。
4. **开始**：此步骤使用上一步骤中处理的数据创建 Azure 虚拟机。

### <a name="failover-timing"></a>故障转移时间

在以下情况下，故障转移需要执行额外的中间步骤，这通常需要花费大约 8 到 10 分钟才能完成：

* VMware VM 运行的移动服务版本低于 9.8
* 物理服务器
* VMware Linux VM
* 作为物理服务器受到保护的 Hyper-V VM
* 其中的下列驱动程序不是启动驱动程序的 VMware VM：
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* 未启用 DHCP 服务的 VMware VM，无论它们使用的是 DHCP 还是静态 IP 地址。

在其他所有情况下，不需要执行中间步骤，因此故障转移花费的时间大大减少。


## <a name="create-a-network-for-test-failover"></a>创建用于测试故障转移的网络

对于测试故障转移，我们建议选择与每个 VM 的“计算和网络”设置中指定的生产恢复站点网络相互独立的网络。 默认情况下，创建 Azure 虚拟网络时，该网络独立于其他网络。 测试网络应模拟生产网络：

- 测试网络中的子网数目应与生产网络中的子网数目相同。 这些子网的名称应该相同。
- 测试网络应使用相同的 IP 地址范围。
- 使用“计算和网络”设置中为 DNS VM 指定的 IP 地址更新测试网络的 DNS。 有关更多详细信息，请参阅 [Active Directory 的测试性故障转移注意事项](site-recovery-active-directory.md#test-failover-considerations)。


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>在恢复站点中执行到生产网络的测试故障转移

尽管我们建议选择与生产网络不同的测试网络，但是，如果确实想要测试到生产网络的灾难恢复演练，请注意以下几点： 

- 确保在运行测试故障转移时主 VM 已关闭。 否则，同一网络中会同时运行两个具有相同标识的 VM。 这可能会导致意外的后果。
- 清理故障转移时，为测试故障转移创建的 VM 发生的任何更改都会丢失。 这些更改不会复制回到主 VM。
- 在生产环境中执行测试会导致生产应用程序关闭。 测试故障转移正在进行时，用户不应使用 VM 上运行的应用。  



## <a name="prepare-active-directory-and-dns"></a>准备 Active Directory 和 DNS

若要运行测试故障转移以进行应用程序测试，需在测试环境中创建 Active Directory 生产环境的副本。 有关详细信息，请参阅 [Active Directory 的测试性故障转移注意事项](site-recovery-active-directory.md#test-failover-considerations)。

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

如果想要在故障转移后使用 RDP 连接到 Azure VM，请遵照以下表格中汇总的要求。

**故障转移** | **位置** | **操作**
--- | --- | ---
**运行 Windows 的 Azure VM** | 故障转移之前的本地计算机 | 若要通过 Internet 访问 Azure VM，请启用 RDP，并确保已针对“公共”添加 TCP 和 UDP 规则，并在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP。<br/><br/> 若要通过站点到站点连接访问 Azure VM，请在计算机上启用 RDP，并确保在“Windows 防火墙” -> “允许的应用和功能”中针对“域和专用”网络允许 RDP。<br/><br/>  确保操作系统 SAN 策略已设置为 **OnlineAll**。 [了解详细信息](https://support.microsoft.com/kb/3031135)。<br/><br/> 在触发故障转移时，请确保 VM 上没有处于挂起状态的 Windows 更新。 Windows 更新可能会在故障转移时启动，在更新完成之前，无法登录到 VM。 
**运行 Windows 的 Azure VM** | 故障转移后的 Azure VM |  为 VM [添加公共 IP 地址](https://aka.ms/addpublicip)。<br/><br/> 已故障转移的 VM（及其连接到的 Azure 子网）上的网络安全组规则需要允许与 RDP 端口建立传入连接。<br/><br/> 选中“启动诊断”可查看 VM 的屏幕截图。<br/><br/> 如果无法连接，请检查 VM 是否正在运行，并查看这些[故障排除提示](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。
**运行 Linux 的 Azure VM** | 故障转移之前的本地计算机 | 确保 VM 上的安全外壳服务已设置为在系统引导时自动启动。<br/><br/> 确保防火墙规则允许 SSH 连接。
**运行 Linux 的 Azure VM** | 故障转移后的 Azure VM | 已故障转移的 VM（及其连接到的 Azure 子网）上的网络安全组规则需要允许与 SSH 端口建立传入连接。<br/><br/> 为 VM [添加公共 IP 地址](https://aka.ms/addpublicip)。<br/><br/> 选中“启动诊断”可查看 VM 的屏幕截图。<br/><br/>



## <a name="next-steps"></a>后续步骤
完成灾难恢复演练后，详细了解其他类型的[故障转移](site-recovery-failover.md)。
