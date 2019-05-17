---
title: 有关 Azure 的 NetApp 文件常见问题 |Microsoft Docs
description: 有关 Azure NetApp 文件常见问题的解答。
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806381"
---
# <a name="faqs-about-azure-netapp-files"></a>有关 Azure 的 NetApp 文件常见问题

本文回答了常见问题 (Faq) 了解 Azure NetApp 文件。 

## <a name="networking-faqs"></a>网络常见问题

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 数据路径经由 Internet？  

不。 NFS 数据路径不需要通过 Internet。 Azure 的 NetApp 文件是一项 Azure 本机服务部署到该服务已上市的 Azure 虚拟网络 (VNet)。 Azure 的 NetApp 文件使用委派的子网，并设置直接在 VNet 上的网络接口。 

请参阅[准则 Azure NetApp 文件的网络规划](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)有关详细信息。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>可以将我已创建的 VNet 连接到 Azure 的 NetApp 文件服务

是的您可以将创建的 Vnet 连接到服务。 

请参阅[准则 Azure NetApp 文件的网络规划](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)有关详细信息。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>是否可以装载 Azure NetApp 文件使用的 DNS FQDN 名称 NFS 的卷？

是的您可以，如果你创建所需的 DNS 条目。 Azure 的 NetApp 文件提供了预配的卷的服务 IP。 

> [!NOTE] 
> 根据需要 azure 的 NetApp 文件可以部署更多服务的 Ip。  DNS 条目可能需要定期更新。

## <a name="security-faqs"></a>安全常见问题

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>可以将 Azure VM 和存储之间的网络流量进行加密？

数据流量 （从 NFSv3 或 SMBv3 客户端到 Azure 的 NetApp 文件卷流量） 不会加密。 但是，从 Azure VM （正在运行的 NFS 或 SMB 客户端） 到 Azure 的 NetApp 文件的通信是与 Azure VM 到 VM 的其他任何流量一样安全。 此流量为本地到 Azure 数据中心网络。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>可以静态加密存储？

所有 Azure NetApp 文件卷都已使用 FIPS 140-2 标准。 所有密钥都管理由 Azure NetApp 文件服务。 

### <a name="how-are-encryption-keys-managed"></a>如何管理加密密钥？ 

Azure NetApp 文件的密钥管理由服务处理。  目前，不支持用户管理的密钥 （自带您自己的密钥）。

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>可以配置 NFS 导出策略规则来控制对 Azure NetApp 文件服务装载目标访问吗？


是的可以在单个 NFS 导出策略中配置最多五个规则。

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp 文件是否支持网络安全组？

否，当前你不能应用网络安全组委派的子网的 Azure NetApp 文件或服务创建的网络接口。

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>可以使用 Azure NetApp 文件使用 Azure IAM？

是的 Azure NetApp 文件支持 Azure IAM RBAC 功能支持。

## <a name="performance-faqs"></a>性能常见问题解答

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>应该如何优化或优化 Azure NetApp 文件性能？

您可以执行以下操作将根据性能要求： 
- 请确保虚拟机大小合适。
- 启用加速的网络的 vm。
- 选择所需的服务级别和容量池的大小。
- 创建具有所需的配额大小的容量和性能的卷。

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>如何将基于吞吐量的服务级别的 Azure NetApp 文件转换为 IOPS？

您可以通过使用下面的公式转换为 IOPS 的 MB/秒：  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>如何更改卷的服务级别？

当前不支持更改卷的服务级别。

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>如何监视 Azure NetApp 文件性能？

Azure 的 NetApp 文件提供了卷性能指标。 此外可用于 Azure Monitor 对 Azure NetApp 文件的监视使用情况指标。  请参阅[用于 Azure 的 NetApp 文件指标](azure-netapp-files-metrics.md)Azure NetApp 文件的性能指标的列表。

## <a name="nfs-faqs"></a>NFS 常见问题解答

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>我想要启动或重启 Azure VM 时自动装载的卷。  如何配置我的主机的永久性 NFS 卷？

若要在 VM 启动或重新启动上自动装载 NFS 卷，将添加一个条目`/etc/fstab`在主机上的文件。 

例如： `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    Azure NetApp 文件卷在卷属性边栏选项卡中找到的 IP 地址
- $FILEPATH  
    Azure NetApp 文件卷导出路径
- $MOUNTPOINT  
    创建用于装载 NFS 导出在 Linux 主机上的目录

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>为什么 DF 命令 NFS 客户端上的不显示预配的卷大小？

DF 中报告的卷大小是 Azure NetApp 文件卷可以增长到的最大大小。 DF 命令中的 Azure NetApp 文件卷的大小不反映的配额或卷的大小。  可以通过 Azure 门户或 API 来获取 Azure NetApp 文件卷的大小或配额。

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp 文件支持 NFS 版本？

Azure 的 NetApp 文件目前支持 NFSv3。

### <a name="how-do-i-enable-root-squashing"></a>如何启用根修复？

目前不支持修复的根。

## <a name="smb-faqs"></a>SMB 常见问题解答

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp 文件是否支持 Azure Active Directory？

不可以，目前不支持它。  Azure 的 NetApp 文件支持 Active Directory 域服务 （自带自己 AD），可以使用 Azure NetApp 文件使用现有的 Active Directory 域控制器。 域控制器可以驻留在 Azure 中作为虚拟机或在本地通过 ExpressRoute。

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>是所需的 SMB 访问权限的 Active Directory 连接？ 

是的必须创建部署的 SMB 卷之前的 Active Directory 连接。 指定的域控制器必须是可由 Azure NetApp 文件成功连接的委派子网访问。  请参阅[创建的 SMB 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume)有关详细信息。 

### <a name="how-many-active-directory-connections-are-supported"></a>支持多少个 Active Directory 连接？

Azure 的 NetApp 文件目前支持每个订阅一个 Active Directory 连接。 此外，Active Directory 连接是特定于单个 NetApp 帐户;它不跨帐户共享。 

### <a name="what-versions-of-windows-ad-are-supported"></a>支持哪些版本的 Windows AD？

Azure 的 NetApp 文件支持的 Active Directory 域服务的 Windows Server 2016 2008r2SP1 的版本。

## <a name="capacity-management-faqs"></a>容量管理常见问题解答

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>如何监视容量池和卷的 Azure NetApp 文件使用情况？ 

Azure 的 NetApp 文件提供了容量池和卷的使用情况度量值。 此外可以使用 Azure Monitor 来监视 Azure NetApp 文件使用情况。 请参阅[用于 Azure 的 NetApp 文件指标](azure-netapp-files-metrics.md)有关详细信息。 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>可以管理通过 Azure 存储资源管理器的 Azure NetApp 文件？

不。 Azure 存储资源管理器不支持 azure 的 NetApp 文件。

## <a name="data-migration-and-protection-faqs"></a>数据迁移和保护常见问题解答

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>如何将数据迁移到 Azure 的 NetApp 文件？
Azure 的 NetApp 文件提供了 NFS 和 SMB 卷。  可以使用任何基于文件的复制工具将数据迁移到该服务。 

NetApp 提供基于 SaaS 的解决方案[NetApp 云同步](https://cloud.netapp.com/cloud-sync-service)。该解决方案，可复制 NFS 或 SMB 数据到 Azure 的 NetApp 文件 NFS 导出或 SMB 共享。 

广泛的免费工具还可用于将数据复制。 Nfs，您可以使用工作负荷工具如[rsync](https://rsync.samba.org/examples.html)复制并将源数据同步到 Azure 的 NetApp 文件卷。 对于 SMB，您可以使用工作负荷[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)相同的方式。  这些工具还可以复制文件或文件夹的权限。 

数据迁移的本地到 Azure 的 NetApp 文件的要求如下所示： 

- 请确保 Azure NetApp 文件是在目标 Azure 区域中可用。
- 验证在源和 Azure NetApp 文件目标卷 IP 地址之间的网络连接。 在本地和 Azure NetApp 文件服务之间的数据传输是通过 ExpressRoute 提供支持。
- 创建目标 Azure NetApp 文件卷。
- 使用您首选的文件复制工具，将源数据传输到目标卷上。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>如何在另一个 Azure 区域中创建一份 Azure NetApp 文件卷？
    
Azure 的 NetApp 文件提供了 NFS 和 SMB 卷。  文件基于复制的任何工具可以用于 Azure 区域之间复制数据。 

NetApp 提供基于 SaaS 解决方案[NetApp 云同步](https://cloud.netapp.com/cloud-sync-service)。该解决方案，可复制 NFS 或 SMB 数据到 Azure 的 NetApp 文件 NFS 导出或 SMB 共享。 

广泛的免费工具还可用于将数据复制。 Nfs，您可以使用工作负荷工具如[rsync](https://rsync.samba.org/examples.html)复制并将源数据同步到 Azure 的 NetApp 文件卷。 对于 SMB，您可以使用工作负荷[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)相同的方式。  这些工具还可以复制文件或文件夹的权限。 

将 Azure NetApp 文件卷复制到另一个 Azure 区域的要求如下所示： 
- 请确保 Azure NetApp 文件是在目标 Azure 区域中可用。
- 验证每个区域中的 Vnet 之间的网络连接。 目前，不支持全局对等互连的 Vnet 之间。  您可以建立一条 ExpressRoute 线路与链接或使用 S2S VPN 连接的 Vnet 之间的连接。 
- 创建目标 Azure NetApp 文件卷。
- 使用您首选的文件复制工具，将源数据传输到目标卷上。

### <a name="is-migration-with-azure-data-box-supported"></a>使用 Azure Data Box 支持是迁移？

不。 Azure Data Box 目前不支持 Azure NetApp 文件。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>是与 Azure 导入/导出服务支持迁移？

不。 Azure 导入/导出服务当前不支持 Azure NetApp 文件。

## <a name="next-steps"></a>后续步骤  

- [Microsoft Azure ExpressRoute 常见问题解答](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure 虚拟网络常见问题](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)