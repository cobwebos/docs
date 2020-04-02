---
title: 关于 Azure NetApp 文件的常见问题解答 |微软文档
description: 回答有关 Azure NetApp 文件常见问题的解答。
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
ms.date: 04/01/2020
ms.author: b-juche
ms.openlocfilehash: 59453dbb9617f5a2ddbdca08709747b11a688560
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547086"
---
# <a name="faqs-about-azure-netapp-files"></a>关于 Azure NetApp 文件的常见问题解答

本文回答了有关 Azure NetApp 文件的常见问题 （FAQ）。 

## <a name="networking-faqs"></a>网络常见问题解答

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 数据路径是否通过 Internet？  

不是。 NFS 数据路径不会通过 Internet。 Azure NetApp 文件是一种 Azure 本机服务，可部署到该服务可用的 Azure 虚拟网络 （VNet） 中。 Azure NetApp 文件使用委派的子网，并直接在 VNet 上提供网络接口。 

有关详细信息[，请参阅 Azure NetApp 文件网络规划指南](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>是否可以将已创建的 VNet 连接到 Azure NetApp 文件服务？

可以，您可以将创建的 VNet 连接到服务。 

有关详细信息[，请参阅 Azure NetApp 文件网络规划指南](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>我能否使用 DNS FQDN 名称装载 Azure NetApp 文件的 NFS 卷？

是的，如果创建所需的 DNS 条目，则可以。 Azure NetApp 文件为预配卷提供服务 IP。 

> [!NOTE] 
> Azure NetApp 文件可以根据需要为服务部署其他 IP。  可能需要定期更新 DNS 条目。

## <a name="security-faqs"></a>安全常见问题

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>是否可以加密 Azure VM 和存储之间的网络流量？

数据流量（来自 NFSv3、NFSv4.1 或 SMBv3 客户端到 Azure NetApp 文件卷的流量）未加密。 但是，从 Azure VM（运行 NFS 或 SMB 客户端）到 Azure NetApp 文件的流量与任何其他 Azure VM 到 VM 的流量一样安全。 此流量是 Azure 数据中心网络的本地流量。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>存储是否可以在静态时加密？

所有 Azure NetApp 文件卷都使用 FIPS 140-2 标准进行加密。 所有密钥都由 Azure NetApp 文件服务管理。 

### <a name="how-are-encryption-keys-managed"></a>如何管理加密密钥？ 

Azure NetApp 文件的关键管理由服务处理。 为每个卷生成唯一的 XTS-AES-256 数据加密密钥。 加密密钥层次结构用于加密和保护所有卷密钥。 这些加密密钥永远不会以未加密的格式显示或报告。 删除卷时，将立即删除加密密钥。

在美国东部、美国西部 2 和美国中南部区域，使用 Azure 专用 HSM 对用户管理密钥（自带密钥）的支持在受控基础上提供。  您可以在 请求访问。 **anffeedback@microsoft.com** 当容量可用时，请求将被批准。

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>是否可以配置 NFS 导出策略规则来控制对 Azure NetApp 文件服务装载目标的访问？


可以，您可以在单个 NFS 导出策略中配置最多五个规则。

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp 文件是否支持网络安全组？

否，当前无法将网络安全组应用于 Azure NetApp 文件的授权子网或服务创建的网络接口。

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>是否可以将 Azure IAM 与 Azure NetApp 文件一起使用？

是的，Azure NetApp 文件支持具有 Azure IAM 的 RBAC 功能。

## <a name="performance-faqs"></a>性能常见问题解答

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>应该如何优化或调整 Azure NetApp 文件性能？

您可以根据性能要求执行以下操作： 
- 确保虚拟机的大小正确。
- 为 VM 启用加速网络。
- 为容量池选择所需的服务级别和大小。
- 创建具有所需配额大小的容量和性能的卷。

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>如何将 Azure NetApp 文件的基于吞吐量的服务级别转换为 IOPS？

您可以使用以下公式将 MB/s 转换为 IOPS：  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>如何更改卷的服务级别？

当前不支持更改卷的服务级别。

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>如何监视 Azure NetApp 文件性能？

Azure NetApp 文件提供卷性能指标。 您还可以使用 Azure 监视器监视 Azure NetApp 文件的使用指标。  有关 Azure NetApp 文件的性能指标列表，请参阅[Azure NetApp 文件的指标](azure-netapp-files-metrics.md)。

## <a name="nfs-faqs"></a>NFS 常见问题解答

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>我希望在启动或重新启动 Azure VM 时自动装载卷。  如何为持久性 NFS 卷配置主机？

要在 VM 启动或重新启动时自动装载 NFS 卷，请将条目`/etc/fstab`添加到主机上的文件。 

有关详细信息[，请参阅装载或卸载 Windows 或 Linux 虚拟机的卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>为什么 NFS 客户端上的 DF 命令不显示预配的卷大小？

DF 中报告的卷大小是 Azure NetApp 文件卷可以增长到的最大大小。 DF 命令中的 Azure NetApp 文件卷的大小不反映卷的配额或大小。  您可以通过 Azure 门户或 API 获取 Azure NetApp 文件卷大小或配额。

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp 文件支持哪些 NFS 版本？

Azure NetApp 文件支持 NFSv3 和 NFSv4.1。 您可以使用任一 NFS 版本[创建卷](azure-netapp-files-create-volumes.md)。 

### <a name="how-do-i-enable-root-squashing"></a>如何启用根挤压？

根挤压当前不受支持。

## <a name="smb-faqs"></a>中小企业常见问题解答

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 访问是否需要活动目录连接？ 

是的，在部署 SMB 卷之前，必须创建活动目录连接。 指定的域控制器必须由 Azure NetApp 文件的委派子网访问，才能成功连接。  有关详细信息[，请参阅创建 SMB 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)。 

### <a name="how-many-active-directory-connections-are-supported"></a>支持多少个活动目录连接？

Azure NetApp 文件不支持单个*区域*中的多个活动目录 （AD） 连接，即使 AD 连接位于不同的 NetApp 帐户中也是如此。 但是，只要 AD 连接位于不同的区域，就可以在单个*订阅*中具有多个 AD 连接。 如果单个区域中需要多个 AD 连接，则可以使用单独的订阅来执行此操作。 

每个 NetApp 帐户配置 AD 连接;AD 连接仅通过其创建的 NetApp 帐户可见。

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp 文件是否支持 Azure 活动目录？ 

支持[Azure 活动目录 （AD） 域服务和](https://docs.microsoft.com/azure/active-directory-domain-services/overview)[活动目录域服务 （AD DS）。](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 您可以将现有的活动目录域控制器与 Azure NetApp 文件一起使用。 域控制器可以作为虚拟机驻留在 Azure 中，也可以通过 ExpressRoute 或 S2S VPN驻留在内部。 Azure NetApp 文件目前不支持[Azure 活动目录](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/)的 AD 联接。

如果将 Azure NetApp 文件与 Azure 活动目录域服务一起使用，则`OU=AADDC Computers`组织单位路径是为 NetApp 帐户配置活动目录时。

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>支持哪些版本的 Windows 服务器活动目录？

Azure NetApp 文件支持 Windows 服务器 2008r2SP1-2019 版本的活动目录域服务。

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>为什么我的 SMB 客户端上的可用空间不显示预配大小？

SMB 客户端报告的卷大小是 Azure NetApp 文件卷可以增长到的最大大小。 SMB 客户端上显示的 Azure NetApp 文件卷的大小不反映卷的配额或大小。 您可以通过 Azure 门户或 API 获取 Azure NetApp 文件卷大小或配额。

### <a name="does-azure-netapp-files-support-kerberos-encryption"></a>Azure NetApp 文件是否支持 Kerberos 加密？

是的，默认情况下，Azure NetApp 文件支持 AES-128 和 AES-256 加密，用于服务和目标活动目录域控制器之间的流量。 有关要求[，请参阅为 Azure NetApp 文件创建 SMB 卷](azure-netapp-files-create-volumes-smb.md)。 

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>容量管理常见问题解答

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>如何监视容量池和 Azure NetApp 文件的容量使用情况？ 

Azure NetApp 文件提供容量池和卷使用情况指标。 您还可以使用 Azure 监视器监视 Azure NetApp 文件的使用情况。 有关详细信息[，请参阅 Azure NetApp 文件的指标](azure-netapp-files-metrics.md)。 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>我能否通过 Azure 存储资源管理器管理 Azure NetApp 文件？

不是。 Azure 存储资源管理器不支持 Azure NetApp 文件。

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>如何确定目录是否接近限制大小？

您可以使用客户端的命令`stat`查看目录是否接近最大大小限制 （320 MB）。

对于 320 MB 目录，块数为 655360，每个块大小为 512 字节。  （即 320x1024x1024/512。  

示例：

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>数据迁移和保护常见问题解答

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>如何将数据迁移到 Azure NetApp 文件？
Azure NetApp 文件提供 NFS 和 SMB 卷。  您可以使用任何基于文件的副本工具将数据迁移到服务。 

NetApp 提供基于 SaaS 的解决方案[，NetApp 云同步](https://cloud.netapp.com/cloud-sync-service)。 该解决方案使您能够将 NFS 或 SMB 数据复制到 Azure NetApp 文件 NFS 导出或 SMB 共享。 

您还可以使用各种免费工具来复制数据。 对于 NFS，可以使用工作负荷工具（如[rsync）](https://rsync.samba.org/examples.html)将源数据复制并同步到 Azure NetApp 文件卷中。 对于 SMB，您可以以同样的方式使用工作负载[机器人。](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)  这些工具还可以复制文件或文件夹权限。 

数据从本地迁移到 Azure NetApp 文件的要求如下： 

- 确保 Azure NetApp 文件在目标 Azure 区域中可用。
- 验证源和 Azure NetApp 文件目标卷 IP 地址之间的网络连接。 通过 ExpressRoute 支持本地和 Azure NetApp 文件服务之间的数据传输。
- 创建目标 Azure NetApp 文件卷。
- 使用首选文件复制工具将源数据传输到目标卷。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>如何在另一个 Azure 区域中创建 Azure NetApp 文件卷的副本？
    
Azure NetApp 文件提供 NFS 和 SMB 卷。  任何基于文件的副本工具都可用于在 Azure 区域之间复制数据。 

NetApp 提供基于 SaaS 的解决方案[，NetApp 云同步](https://cloud.netapp.com/cloud-sync-service)。 该解决方案使您能够将 NFS 或 SMB 数据复制到 Azure NetApp 文件 NFS 导出或 SMB 共享。 

您还可以使用各种免费工具来复制数据。 对于 NFS，可以使用工作负荷工具（如[rsync）](https://rsync.samba.org/examples.html)将源数据复制并同步到 Azure NetApp 文件卷中。 对于 SMB，您可以以同样的方式使用工作负载[机器人。](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)  这些工具还可以复制文件或文件夹权限。 

将 Azure NetApp 文件卷复制到其他 Azure 区域的要求如下： 
- 确保 Azure NetApp 文件在目标 Azure 区域中可用。
- 验证每个区域中的 VNet 之间的网络连接。 目前，不支持 VNet 之间的全局对等互连。  您可以通过与 ExpressRoute 电路链接或使用 S2S VPN 连接来建立 VNet 之间的连接。 
- 创建目标 Azure NetApp 文件卷。
- 使用首选文件复制工具将源数据传输到目标卷。

### <a name="is-migration-with-azure-data-box-supported"></a>是否支持使用 Azure 数据框进行迁移？

不是。 Azure 数据框当前不支持 Azure NetApp 文件。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>是否支持 Azure 导入/导出服务的迁移？

不是。 Azure 导入/导出服务当前不支持 Azure NetApp 文件。

## <a name="next-steps"></a>后续步骤  

- [微软 Azure 快速路由常见问题解答](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [微软 Azure 虚拟网络常见问题解答](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [有关 Azure NetApp 文件的 SMB 性能的常见问题解答](azure-netapp-files-smb-performance.md)
