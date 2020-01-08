---
title: 有关 Azure NetApp 文件的常见问题解答 |Microsoft Docs
description: 解答有关 Azure NetApp 文件的常见问题。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: b-juche
ms.openlocfilehash: 630535103656e13ca43d958ef2a4124a4ea940ac
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658281"
---
# <a name="faqs-about-azure-netapp-files"></a>有关 Azure NetApp 文件的常见问题解答

本文解答有关 Azure NetApp 文件的常见问题（Faq）。 

## <a name="networking-faqs"></a>网络常见问题解答

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 数据路径是否通过 Internet？  

不。 NFS 数据路径不通过 Internet。 Azure NetApp 文件是部署到 Azure 虚拟网络（VNet）中的 azure 本机服务，该服务可用。 Azure NetApp 文件使用委托子网，并直接在 VNet 中预配网络接口。 

有关详细信息，请参阅[Azure NetApp 文件的准则网络规划](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>是否可以将已创建的 VNet 连接到 Azure NetApp 文件服务？

是的，可以将创建的 Vnet 连接到服务。 

有关详细信息，请参阅[Azure NetApp 文件的准则网络规划](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>是否可以使用 DNS FQDN 名称装载 Azure NetApp 文件的 NFS 卷？

是的，如果您创建了所需的 DNS 条目，可以这样做。 Azure NetApp 文件提供预配卷的服务 IP。 

> [!NOTE] 
> Azure NetApp 文件可根据需要部署服务的其他 Ip。  DNS 条目可能需要定期更新。

## <a name="security-faqs"></a>安全常见问题

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM 和存储之间的网络流量是否可以加密？

未加密数据流量（从 NFSv3、NFSv 4.1 或 SMBv3 客户端到 Azure NetApp 文件卷的流量）。 但是，从 Azure VM （运行 NFS 或 SMB 客户端）到 Azure NetApp 文件的流量与任何其他 Azure VM 到 VM 的流量一样安全。 此流量是 Azure 数据中心网络的本地流量。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>是否可以静态加密存储？

所有 Azure NetApp 文件卷都是使用 FIPS 140-2 标准进行加密的。 所有密钥由 Azure NetApp 文件服务管理。 

### <a name="how-are-encryption-keys-managed"></a>如何管理加密密钥？ 

Azure NetApp 文件的密钥管理由服务处理。 为每个卷生成唯一的 XTS-256 数据加密密钥。 加密密钥层次结构用于加密和保护所有卷密钥。 这些加密密钥永远不会以未加密的格式显示或报告。 删除卷时，会立即删除加密密钥。

目前不支持用户托管的密钥（自带密钥）。

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>能否配置 NFS 导出策略规则来控制对 Azure NetApp 文件服务装载目标的访问？


是的，你可以在单个 NFS 导出策略中配置最多五个规则。

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp 文件是否支持网络安全组？

不可以，当前无法将网络安全组应用于 Azure NetApp 文件的委托子网或服务创建的网络接口。

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>能否在 Azure NetApp 文件中使用 Azure IAM？

是的，Azure NetApp 文件支持通过 Azure IAM 提供 RBAC 功能。

## <a name="performance-faqs"></a>性能常见问题解答

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>我应该如何优化或调整 Azure NetApp 文件性能？

您可以根据性能要求执行以下操作： 
- 请确保虚拟机大小合适。
- 为 VM 启用加速网络。
- 选择所需的服务级别和容量池大小。
- 创建具有所需配额大小的卷来存储容量和性能。

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>如何实现将 Azure NetApp 文件的基于吞吐量的服务级别转换为 IOPS？

可以使用以下公式将 MB/秒转换为 IOPS：  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>如何实现更改卷的服务级别？

当前不支持更改卷的服务级别。

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>如何实现监视 Azure NetApp 文件性能？

Azure NetApp 文件提供了卷性能指标。 你还可以使用 Azure Monitor 来监视 Azure NetApp 文件的使用情况指标。  有关 Azure NetApp 文件的性能指标列表，请参阅[Azure Netapp 文件的指标](azure-netapp-files-metrics.md)。

## <a name="nfs-faqs"></a>NFS 常见问题

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>我希望在启动或重新启动 Azure VM 时自动装载卷。  如何实现为持久性 NFS 卷配置主机？

要使 NFS 卷在 VM 启动或重新启动时自动装载，请将条目添加到主机上的 `/etc/fstab` 文件中。 

有关详细信息，请参阅[装入或卸载适用于 Windows 或 Linux 虚拟机的卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>为什么 NFS 客户端上的 DF 命令不显示预配的卷大小？

DF 中报告的卷大小是 Azure NetApp 文件量可以增长到的最大大小。 DF 命令中的 Azure NetApp 文件量的大小不反映卷的配额或大小。  可以通过 Azure 门户或 API 获取 Azure NetApp 文件的容量或配额。

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp 文件支持什么 NFS 版本？

Azure NetApp 文件支持 NFSv3 和 NFSv 4.1。 可以使用任一 NFS 版本创建卷。 

> [!IMPORTANT] 
> 访问 NFSv4.1 功能需要加入允许列表。  若要请求加入允许列表，请将请求提交到 <anffeedback@microsoft.com>。 


### <a name="how-do-i-enable-root-squashing"></a>如何实现启用根 squashing？

当前不支持根 squashing。

## <a name="smb-faqs"></a>SMB 常见问题解答

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 访问是否需要 Active Directory 连接？ 

是的，你必须在部署 SMB 卷之前创建 Active Directory 连接。 若要成功连接，Azure NetApp 文件的委托子网必须可以访问指定的域控制器。  有关详细信息，请参阅[创建 SMB 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)。 

### <a name="how-many-active-directory-connections-are-supported"></a>支持多少个 Active Directory 连接？

目前，对于每个 NetApp 帐户、每个订阅和每个区域，Azure NetApp 文件仅支持一个 Active Directory 连接;不会在 NetApp 帐户之间共享连接。

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp 文件是否支持 Azure Active Directory？ 

支持[Azure Active Directory （AD）域服务](https://docs.microsoft.com/azure/active-directory-domain-services/overview)和[Active Directory 域服务（AD DS）](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 。 可以将现有 Active Directory 域控制器与 Azure NetApp 文件一起使用。 域控制器可以作为虚拟机或本地通过 ExpressRoute 或 S2S VPN 驻留在 Azure 中。 目前，Azure NetApp 文件不支持[Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)的 AD join。

如果将 Azure NetApp 文件与 Azure Active Directory 域服务一起使用，则在为 NetApp 帐户配置 Active Directory 时，将 `OU=AADDC Computers` 组织单位路径。

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>支持哪些版本的 Windows Server Active Directory？

Azure NetApp 文件支持 Windows Server 2008r2SP1-2019 版本的 Active Directory 域服务。

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>为什么我的 SMB 客户端上的可用空间不显示预配的大小？

SMB 客户端报告的卷大小是 Azure NetApp 文件量可以增长到的最大大小。 SMB 客户端上显示的 Azure NetApp 文件卷的大小不会反映卷的配额或大小。 可以通过 Azure 门户或 API 获取 Azure NetApp 文件的容量或配额。

## <a name="capacity-management-faqs"></a>容量管理常见问题

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>如何实现监视 Azure NetApp 文件的容量池和容量的使用情况？ 

Azure NetApp 文件提供容量池和卷使用情况指标。 你还可以使用 Azure Monitor 来监视 Azure NetApp 文件的使用情况。 有关详细信息，请参阅[Azure NetApp 文件的指标](azure-netapp-files-metrics.md)。 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>是否可以通过 Azure 存储资源管理器管理 Azure NetApp 文件？

不。 Azure 存储资源管理器不支持 Azure NetApp 文件。

## <a name="data-migration-and-protection-faqs"></a>数据迁移和保护常见问题解答

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>如何实现将数据迁移到 Azure NetApp 文件？
Azure NetApp 文件提供 NFS 和 SMB 卷。  你可以使用任何基于文件的复制工具将数据迁移到服务。 

NetApp 提供基于 SaaS 的解决方案，即[Netapp 云同步](https://cloud.netapp.com/cloud-sync-service)。 解决方案使你能够将 NFS 或 SMB 数据复制到 Azure NetApp 文件 NFS 导出或 SMB 共享。 

你还可以使用广泛的免费工具来复制数据。 对于 NFS，可以使用工作负荷工具（如[rsync](https://rsync.samba.org/examples.html) ）将源数据复制和同步到 Azure NetApp 文件量。 对于 SMB，可以采用相同的方式使用工作负荷[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 。  这些工具还可以复制文件或文件夹的权限。 

从本地到 Azure NetApp 文件的数据迁移要求如下所示： 

- 确保 Azure NetApp 文件在目标 Azure 区域中可用。
- 验证源和 Azure NetApp 文件目标卷 IP 地址之间的网络连接。 本地和 Azure NetApp 文件服务之间的数据传输通过 ExpressRoute 支持。
- 创建目标 Azure NetApp 文件卷。
- 使用首选的文件复制工具将源数据传输到目标卷。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>如何实现在另一个 Azure 区域创建 Azure NetApp 文件卷的副本？
    
Azure NetApp 文件提供 NFS 和 SMB 卷。  可以使用任何基于文件的复制工具在 Azure 区域之间复制数据。 

NetApp 提供基于 SaaS 的解决方案，即[Netapp 云同步](https://cloud.netapp.com/cloud-sync-service)。 解决方案使你能够将 NFS 或 SMB 数据复制到 Azure NetApp 文件 NFS 导出或 SMB 共享。 

你还可以使用广泛的免费工具来复制数据。 对于 NFS，可以使用工作负荷工具（如[rsync](https://rsync.samba.org/examples.html) ）将源数据复制和同步到 Azure NetApp 文件量。 对于 SMB，可以采用相同的方式使用工作负荷[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 。  这些工具还可以复制文件或文件夹的权限。 

将 Azure NetApp 文件卷复制到其他 Azure 区域的要求如下： 
- 确保 Azure NetApp 文件在目标 Azure 区域中可用。
- 验证每个区域中 Vnet 之间的网络连接。 目前，不支持 Vnet 之间的全局对等互连。  可以通过使用 ExpressRoute 线路或使用 S2S VPN 连接来建立 Vnet 之间的连接。 
- 创建目标 Azure NetApp 文件卷。
- 使用首选的文件复制工具将源数据传输到目标卷。

### <a name="is-migration-with-azure-data-box-supported"></a>Azure Data Box 是否支持迁移？

不。 Azure Data Box 目前不支持 Azure NetApp 文件。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>是否支持通过 Azure 导入/导出服务进行迁移？

不。 Azure 导入/导出服务目前不支持 Azure NetApp 文件。

## <a name="next-steps"></a>后续步骤  

- [Microsoft Azure ExpressRoute 常见问题](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure 虚拟网络常见问题](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
