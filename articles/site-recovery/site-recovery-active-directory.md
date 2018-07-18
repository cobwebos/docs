---
title: 使用 Azure Site Recovery 保护 Active Directory 和 DNS | Microsoft 文档
description: 本文介绍如何使用 Azure Site Recovery 为 Active Directory 实现灾难恢复解决方案。
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: e8094c582af6ea03f5ffcc4f61914488891cb556
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920883"
---
# <a name="use-azure-site-recovery-to-protect-active-directory-and-dns"></a>使用 Azure Site Recovery 保护 Active Directory 和 DNS

企业应用程序，例如 SharePoint、Dynamics AX 和 SAP，都依赖于 Active Directory 和 DNS 基础结构才能正常工作。 为应用程序设置灾难恢复时，通常需要先恢复 Active Directory 和 DNS，然后再恢复其他应用程序组件，以确保应用程序功能正常。

可以使用 [Site Recovery](site-recovery-overview.md)为 Active Directory 创建灾难恢复计划。 发生中断时，可启动故障转移。 并可在数分钟内启动并运行 Active Directory。 如果为主站点中的多个应用程序（如 SharePoint 和 SAP）部署了 Active Directory，则可能希望故障转移整个站点。 可先使用 Site Recovery 故障转移 Active Directory。 然后，再使用特定于应用程序的恢复计划故障转移其他应用程序。

本文介绍如何为 Active Directory 创建灾难恢复解决方案。 其中包括先决条件，以及故障转移的说明。 开始之前，应先熟悉 Active Directory 和 Site Recovery。

## <a name="prerequisites"></a>先决条件

* 如果要复制到 Azure，请[准备 Azure 资源](tutorial-prepare-azure.md)，包括订阅、Azure 虚拟网络、存储帐户和恢复服务保管库。
* 查看所有组件的[支持要求](site-recovery-support-matrix-to-azure.md)。

## <a name="replicate-the-domain-controller"></a>复制域控制器

至少需要在一台托管域控制器或 DNS 的 VM 上设置 [Site Recovery 复制](#enable-protection-using-site-recovery)。 如果环境中有[多个域控制器](#environment-with-multiple-domain-controllers)，还必须在目标站点上设置[附加的域控制器](#protect-active-directory-with-active-directory-replication)。 附加的域控制器可以在 Azure 中，也可位于辅助本地数据中心。

### <a name="single-domain-controller"></a>单个域控制器
如果应用程序数目较少并且只有一个域控制器，则可能希望对整个站点进行故障转移。 在这种情况下，我们建议使用 Site Recovery 将域控制器复制到目标站点（无论域控制器位于 Azure 还是辅助本地数据中心）。 也可以将复制的同一个域控制器或 DNS 虚拟机用于[测试故障转移](#test-failover-considerations)。

### <a name="multiple-domain-controllers"></a>多个域控制器
如果应用程序数量较多，而环境中不止一个域控制器，或者计划一次性故障转移多个应用程序，除了使用 Site Recovery 复制域控制器虚拟机以外，我们建议在目标站点（Azure 或辅助本地数据中心）上设置[附加的域控制器](#protect-active-directory-with-active-directory-replication)。 对于[测试故障转移](#test-failover-considerations)，可使用由 Site Recovery 复制的域控制器。 对于故障转移，可以在目标站点上使用附加的域控制器。

## <a name="enable-protection-with-site-recovery"></a>使用 Site Recovery 启用保护

可以使用 Site Recovery 来保护托管域控制器或 DNS 的虚拟机。

### <a name="protect-the-vm"></a>保护 VM
使用 Site Recovery 复制的域控制器用于[测试故障转移](#test-failover-considerations)。 确保它满足以下要求：

1. 域控制器是全局编录服务器。
2. 域控制器应为测试故障转移期间所需角色的 FSMO 角色所有者。 否则，故障转移之后需要[获取](http://aka.ms/ad_seize_fsmo)这些角色。

### <a name="configure-vm-network-settings"></a>配置 VM 网络设置
对于托管域控制器或 DNS 的虚拟机，请在 Site Recovery 中在复制的虚拟机的“计算和网络”设置下配置网络设置。 这可确保故障转移后虚拟机附加到正确的网络。

## <a name="protect-active-directory"></a>保护 Active Directory

### <a name="site-to-site-protection"></a>站点到站点保护
在辅助站点上创建域控制器。 将服务器提升为域控制器角色时，请指定在主站点中使用的同一域名。 可以使用 **Active Directory 站点和服务**管理单元来配置站点要添加到的站点链接对象的设置。 通过在站点链接上配置设置，可以控制何时在两个或更多站点之间进行复制，以及复制的频率。 有关详细信息，请参阅[计划站点之间的复制](https://technet.microsoft.com/library/cc731862.aspx)。

### <a name="site-to-azure-protection"></a>站点到 Azure 的保护
首先，在 Azure 虚拟网络中创建域控制器。 将服务器提升为域控制器角色时，请指定主站点中使用的同一域名。

然后，为虚拟网络重新配置 DNS 服务器以在 Azure 中使用 DNS 服务器。

![Azure 网络](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure 到 Azure 的保护
首先，在 Azure 虚拟网络中创建域控制器。 将服务器提升为域控制器角色时，请指定主站点中使用的同一域名。

然后，为虚拟网络重新配置 DNS 服务器以在 Azure 中使用 DNS 服务器。

## <a name="test-failover-considerations"></a>测试故障转移注意事项
为避免对生产工作负荷造成影响，测试故障转移在与生产网络隔离的网络中进行。

大多数应用程序需要域控制器和 DNS 服务器。 因此，在对应用程序进行故障转移之前，必须在用于测试故障转移的独立网络中创建域控制器。 最简单的方法是使用 Site Recovery 复制托管域控制器或 DNS 的虚拟机。 然后，在对应用程序的恢复计划运行测试故障转移之前，先对域控制器虚拟机运行测试故障转移。 下面介绍了操作方法：

1. 使用 Site Recovery [复制](vmware-azure-tutorial.md)托管域控制器或 DNS 的虚拟机。
2. 创建独立的网络。 默认情况下，在 Azure 中创建的任何虚拟网络均独立于其他网络。 建议对此网络使用与生产网络相同的 IP 地址范围。 不要在此网络上启用站点到站点连接。
3. 在独立网络中提供 DNS IP 地址。 使用 DNS 虚拟机预计应会获取的 IP 地址。 如果要复制到 Azure，请提供用于故障转移的虚拟机的 IP 地址。 若要输入 IP 地址，请在复制的虚拟机的“计算和网络”设置中，选择“目标 IP”设置。

    ![Azure 测试网络](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery 尝试在名称相同的子网中创建测试虚拟机，并使用虚拟机的“计算与网络”设置中提供的同一 IP 地址。 如果为测试故障转移提供的 Azure 虚拟网络中没有名称相同的子网，则会按字母顺序在第一个子网中创建测试虚拟机。
    >
    > 如果目标 IP 地址归属于所选子网，则 Site Recovery 会尝试使用该目标 IP 地址创建测试故障转移虚拟机。 如果目标 IP 不属于所选子网，则会使用所选子网中下一个可用 IP 创建测试故障转移虚拟机。
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>测试故障转移至辅助站点

1. 如果要复制到其他本地站点并使用 DHCP，请[针对测试故障转移设置 DNS 和 DHCP](hyper-v-vmm-test-failover.md#prepare-dhcp)。
2. 对隔离网络中运行的域控制器虚拟机执行测试故障转移。 使用域控制器虚拟机最新可用的应用程序一致恢复点来执行测试故障转移。
3. 针对包含虚拟机（应用程序在其中运行）的恢复计划运行测试故障转移。
4. 测试完成后，请在域控制器虚拟机上清理测试故障转移。 此步骤删除为测试性故障转移创建的域控制器。


### <a name="remove-references-to-other-domain-controllers"></a>删除对其他域控制器的引用
进行测试故障转移时，不需要将所有域控制器都引入测试网络中。 若要删除生产环境中存在的对其他域控制器的引用，可能需要为缺失的域控制器[获取 FSMO Active Directory 角色](http://aka.ms/ad_seize_fsmo)并执行[元数据清理](https://technet.microsoft.com/library/cc816907.aspx)。


### <a name="issues-caused-by-virtualization-safeguards"></a>虚拟化安全措施引起的问题

> [!IMPORTANT]
> 本部分介绍的某些配置不是标准或默认的域控制器配置。 如果不想对生产域控制器进行此类更改，可创建一个 Site Recovery 专门用于测试故障转移的域控制器。 仅对该域控制器进行此类更改。  
>
>

从 Windows Server 2012 开始，[Active Directory 域服务 (AD DS) 中内置了额外的安全措施](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)。 如果底层虚拟机监控程序平台支持 VM-GenerationID，这些安全措施就可以防止虚拟化域控制器出现 USN 回退。 Azure 支持 VM-GenerationID。 因此，在 Azure 虚拟机上运行 Windows Server 2012 或更高版本的域控制器具有额外的安全防护措施。


重置 VM-GenerationID 时，AD DS 数据库的 InvocationID 值也会被重置。 除此之外，还放弃了 RID 池，将 SYSVOL 标记为非授权。 有关详细信息，请参阅 [Active Directory 域服务虚拟化简介](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)和[安全虚拟化 DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)。

故障转移到 Azure 可能会导致 VM-GenerationID重置。 域控制器虚拟机在 Azure 中启动时，重置 VM-GenerationID会触发额外的安全措施。 尝试登录域控制器虚拟机时，这可能会导致严重的延迟。

由于该域控制器仅用于测试故障转移，因此不需要实施虚拟化安全措施。 要确保域控制器虚拟机的 VM-GenerationID 值不改变，可在本地域控制器中将下述 DWORD 的值更改为 4：


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>虚拟化安全措施的症状

如果在测试故障转移后触发了虚拟化安全措施，则可能会看到下述一项或多项症状：  

* GenerationID 值发生变化。

    ![生成 ID 更改](./media/site-recovery-active-directory/Event2170.png)

* InvocationID 值发生变化。

    ![调用 ID 更改](./media/site-recovery-active-directory/Event1109.png)

* SYSVOL 和 NETLOGON 共享不可用。

    ![SYSVOL 共享](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* DFSR 数据库被删除。

    ![DFSR 数据库被删除](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>在测试故障转移期间排除域控制器问题

> [!IMPORTANT]
> 本部分介绍的某些配置不是标准或默认的域控制器配置。 如果不想对生产域控制器进行此类更改，可创建一个专用于 Site Recovery 测试故障转移的域控制器。 仅对该专用域控制器进行此类更改。  
>
>

1. 在命令提示符处运行以下命令，检查是否已共享 SYSVOL 和 NETLOGON 文件夹：

    `NET SHARE`

2. 在命令提示符处运行以下命令，确保域控制器正常运作：

    `dcdiag /v > dcdiag.txt`

3. 在输出日志中，查找以下文本。 下列文本可用于确认域控制器正常运作。

    * “通过测试连接”
    * “通过测试播发”
    * “通过测试 MachineAccount”

如果满足上述条件，则域控制器很可能运行良好。 否则，请完成以下步骤：

1. 对域控制器执行授权还原。 请牢记以下信息：
    * 虽然不推荐 [FRS 复制](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)，但如果使用了 FRS 复制，请按照步骤执行授权还原。 [使用 BurFlags 注册表项重新初始化文件复制服务](https://support.microsoft.com/kb/290762)中介绍了该过程。

        有关 BurFlags 的详细信息，请参阅博客文章 [D2 和 D4：它的作用是什么？](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/)。
    * 如果使用 DFSR 复制，请完成授权还原步骤。 [强制进行 DFSR 复制的 SYSVOL（如 FRS 的“D4/D2”）的授权和非授权同步](https://support.microsoft.com/kb/2218556)中介绍了该过程。

        还可以使用 PowerShell 函数。 有关详细信息，请参阅 [DFSR-SYSVOL 授权/非授权还原 PowerShell 函数](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/)。

2. 在本地域控制器中将以下注册表项设置为 0，绕过初始同步要求。 如果 DWORD 不存在，可在“Parameters”节点下创建。

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    有关详细信息，请参阅[解决 DNS 事件 ID 4013：DNS 服务器不能加载 AD 集成的 DNS 区域](https://support.microsoft.com/kb/2001093)。

3. 禁用需要全局编录服务器才能验证用户登录的要求。 为此，请在本地域控制器中，将以下注册表项设置为 1。 如果 DWORD 不存在，可在“Lsa”节点下创建。

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    有关详细信息，请参阅[禁用需要全局编录服务器才能验证用户登录的要求](http://support.microsoft.com/kb/241789)。

### <a name="dns-and-domain-controller-on-different-machines"></a>不同计算机上的 DNS 和域控制器
如果 DNS 与域控制器不在同一个虚拟机上，则必须创建一个可以进行测试故障转移的 DNS 虚拟机。 如果 DNS 与域控制器不在同一个虚拟机上，则可以跳过本节。

可以使用全新的 DNS 服务器并创建所有需要的区域。 例如，如果 Active Directory 域是 contoso.com，则可以使用名称 contoso.com 创建 DNS 区域。 必须在 DNS 中更新与 Active Directory 对应的条目，如下所示：

1. 确保在恢复计划中的任何其他虚拟机启动之前，以下设置已准备就绪：
   * 区域必须以林根名称命名。
   * 区域必须备份文件。
   * 必须启用区域以进行安全和非安全更新。
   * 托管域控制器的虚拟机的解析程序应指向 DNS 虚拟机的 IP 地址。

2. 在托管域控制器的 VM 中运行以下命令：

    `nltest /dsregdns`

3. 运行以下命令，在 DNS 服务器上添加区域，允许非安全更新，并向 DNS 添加该区域的条目：

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>后续步骤
详细了解如何[使用 Azure Site Recovery 保护企业工作负荷](site-recovery-workload.md)。
