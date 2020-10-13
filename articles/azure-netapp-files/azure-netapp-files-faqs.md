---
title: 有关 Azure NetApp 文件的常见问题解答 |Microsoft Docs
description: 查看有关 Azure NetApp 文件（如网络、安全性、性能、容量管理和数据迁移/保护）的常见问题。
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2a64e595f0ea07510f416be56a54a3c74294b95d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653615"
---
# <a name="faqs-about-azure-netapp-files"></a>有关 Azure NetApp 文件的常见问题解答

本文解答了有关 Azure NetApp 文件 (常见问题解答) 常见问题。 

## <a name="networking-faqs"></a>网络常见问题解答

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS 数据路径是否通过 Internet？  

不是。 NFS 数据路径不通过 Internet。 Azure NetApp 文件是一个 Azure 本机服务，该服务部署到 Azure 虚拟网络 (VNet) 服务可用。 Azure NetApp 文件使用委托子网，并直接在 VNet 中预配网络接口。 

有关详细信息，请参阅 [Azure NetApp 文件的准则网络规划](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>是否可以将已创建的 VNet 连接到 Azure NetApp 文件服务？

是的，可以将创建的 Vnet 连接到服务。 

有关详细信息，请参阅 [Azure NetApp 文件的准则网络规划](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>是否可以使用 DNS FQDN 名称装载 Azure NetApp 文件的 NFS 卷？

是的，如果您创建了所需的 DNS 条目，可以这样做。 Azure NetApp 文件提供预配卷的服务 IP。 

> [!NOTE] 
> Azure NetApp 文件可根据需要部署服务的其他 Ip。  DNS 条目可能需要定期更新。

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>能否为 Azure NetApp 文件卷设置或选择自己的 IP 地址？  

不是。 IP 分配给 Azure NetApp 文件卷是动态的。 不支持静态 IP 分配。 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp 文件是否支持双堆栈 (IPv4 和 IPv6) VNet？

不能，Azure NetApp 文件当前不支持 (IPv4 和 IPv6) VNet 的双堆栈。  
 
## <a name="security-faqs"></a>安全常见问题

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM 和存储之间的网络流量是否可以加密？

可以使用 Kerberos 和 AES-256 加密在 NFSv 4.1 客户端和 Azure NetApp 文件卷之间进行数据通信。 有关详细信息，请参阅 [Configure nfsv 4.1 Kerberos encryption For Azure NetApp 文件](configure-kerberos-encryption.md) 。   

NFSv3 或 SMBv3 客户端与 Azure NetApp 文件卷之间的数据流量未加密。 但是，与任何其他 Azure VM 到 VM 的流量一样，Azure VM (运行 NFS 或 SMB 客户端) Azure NetApp 文件的流量。 此流量是 Azure 数据中心网络的本地流量。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>是否可以静态加密存储？

所有 Azure NetApp 文件卷都是使用 FIPS 140-2 标准进行加密的。 所有密钥由 Azure NetApp 文件服务管理。 

### <a name="how-are-encryption-keys-managed"></a>如何管理加密密钥？ 

Azure NetApp 文件的密钥管理由服务处理。 为每个卷生成唯一的 XTS-256 数据加密密钥。 加密密钥层次结构用于加密和保护所有卷密钥。 这些加密密钥永远不会以未加密的格式显示或报告。 删除卷时，会立即删除加密密钥。

在美国东部、美国中南部、美国西部2和 US Gov 弗吉尼亚州地区，使用 Azure 专用 HSM (创建自己的密钥) 支持客户管理的密钥。 您可以在上请求访问 [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) 。 容量可用时，将批准请求。

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

可以通过将卷移到使用所需的 [服务级别](azure-netapp-files-service-levels.md) 的其他容量池来更改现有卷的服务级别。 请参阅 [动态更改卷的服务级别](dynamic-change-volume-service-level.md)。 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>如何实现监视 Azure NetApp 文件性能？

Azure NetApp 文件提供了卷性能指标。 你还可以使用 Azure Monitor 来监视 Azure NetApp 文件的使用情况指标。  有关 Azure NetApp 文件的性能指标列表，请参阅 [Azure Netapp 文件的指标](azure-netapp-files-metrics.md) 。

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>NFSv 4.1 上的 Kerberos 对性能有什么影响？

请参阅 [nfsv 4.1 上的 Kerberos 对性能的影响](configure-kerberos-encryption.md#kerberos_performance) ，了解有关 nfsv 4.1 安全选项、测试的性能向量和预期的性能影响的信息。 

## <a name="nfs-faqs"></a>NFS 常见问题解答

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>我希望在启动或重新启动 Azure VM 时自动装载卷。  如何实现为持久性 NFS 卷配置主机？

要使 NFS 卷在 VM 启动或重新启动时自动装载，请在主机上的文件中添加一个条目 `/etc/fstab` 。 

有关详细信息，请参阅 [装入或卸载适用于 Windows 或 Linux 虚拟机的卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 。  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>为什么 NFS 客户端上的 DF 命令不显示预配的卷大小？

DF 中报告的卷大小是 Azure NetApp 文件量可以增长到的最大大小。 DF 命令中的 Azure NetApp 文件量的大小不反映卷的配额或大小。  可以通过 Azure 门户或 API 获取 Azure NetApp 文件的容量或配额。

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp 文件支持什么 NFS 版本？

Azure NetApp 文件支持 NFSv3 和 NFSv 4.1。 可以使用任一 NFS 版本 [创建卷](azure-netapp-files-create-volumes.md) 。 

### <a name="how-do-i-enable-root-squashing"></a>如何实现启用根 squashing？

你可以使用卷的导出策略指定根帐户是否可以访问该卷。 有关详细信息，请参阅 [配置 NFS 卷的导出策略](azure-netapp-files-configure-export-policy.md) 。

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>能否为多个卷 (卷创建令牌) 使用相同的文件路径？

可以。 但是，文件路径必须在不同的订阅或不同的区域中使用。   

例如，创建一个名为的卷 `vol1` 。 然后，在同一订阅和区域中创建另一个也被称为 `vol1` 不同容量池中的卷。 在这种情况下，使用相同的卷名称 `vol1` 将导致错误。 若要使用相同的文件路径，名称必须在不同的区域或订阅中。

## <a name="smb-faqs"></a>SMB 常见问题解答

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Azure NetApp 文件支持哪些 SMB 版本？

Azure NetApp 文件支持 SMB 2.1 和 SMB 3.1 (，其中包括对 SMB 3.0) 的支持。    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB 访问是否需要 Active Directory 连接？ 

是的，你必须在部署 SMB 卷之前创建 Active Directory 连接。 若要成功连接，Azure NetApp 文件的委托子网必须可以访问指定的域控制器。  有关详细信息，请参阅 [创建 SMB 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) 。 

### <a name="how-many-active-directory-connections-are-supported"></a>支持多少个 Active Directory 连接？

即使 AD 连接在不同的 NetApp 帐户中，Azure NetApp 文件也不支持单个 *区域*中 (ad) 连接的多个 Active Directory。 但是，只要 AD 连接位于不同的区域，就可以在单个 *订阅*中拥有多个 ad 连接。 如果在单个区域中需要多个 AD 连接，则可以使用单独的订阅来执行此操作。 

AD 连接是按 NetApp 帐户配置的;AD 连接仅在创建它的 NetApp 帐户中可见。

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp 文件是否支持 Azure Active Directory？ 

支持 [Azure Active Directory (AD) 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/overview) 和 [Active Directory 域服务 (](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) AD DS) 。 可以将现有 Active Directory 域控制器与 Azure NetApp 文件一起使用。 域控制器可以作为虚拟机或本地通过 ExpressRoute 或 S2S VPN 驻留在 Azure 中。 目前，Azure NetApp 文件不支持 [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) 的 AD join。

如果将 Azure NetApp 文件与 Azure Active Directory 域服务一起使用，则在为 NetApp 帐户配置 Active Directory 时，组织单位路径为 `OU=AADDC Computers`。

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>支持哪些版本的 Windows Server Active Directory？

Azure NetApp 文件支持 Windows Server 2008r2SP1-2019 版本的 Active Directory 域服务。

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>为什么我的 SMB 客户端上的可用空间不显示预配的大小？

SMB 客户端报告的卷大小是 Azure NetApp 文件量可以增长到的最大大小。 SMB 客户端上显示的 Azure NetApp 文件卷的大小不会反映卷的配额或大小。 可以通过 Azure 门户或 API 获取 Azure NetApp 文件的容量或配额。

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="dual-protocol-faqs"></a>双重协议常见问题

### <a name="i-tried-to-use-the-root-and-local-users-to-access-a-dual-protocol-volume-with-the-ntfs-security-style-on-a-unix-system-why-did-i-encounter-a-permission-denied-error"></a>我尝试使用 "root" 和本地用户访问 UNIX 系统上具有 NTFS 安全样式的双协议卷。 为什么会出现 "权限被拒绝" 错误？   

有关解决方法，请参阅 [排查双协议卷问题](troubleshoot-dual-protocol-volumes.md) 。

### <a name="when-i-try-to-create-a-dual-protocol-volume-why-does-the-creation-process-fail-with-the-error-failed-to-validate-ldap-configuration-try-again-after-correcting-ldap-configuration"></a>尝试创建双重协议卷时，为什么创建过程失败，并出现错误 "无法验证 LDAP 配置，更正 LDAP 配置后重试"？  

有关解决方法，请参阅 [排查双协议卷问题](troubleshoot-dual-protocol-volumes.md) 。

## <a name="capacity-management-faqs"></a>容量管理常见问题

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>如何实现监视 Azure NetApp 文件的容量池和容量的使用情况？ 

Azure NetApp 文件提供容量池和卷使用情况指标。 你还可以使用 Azure Monitor 来监视 Azure NetApp 文件的使用情况。 有关详细信息，请参阅 [Azure NetApp 文件的指标](azure-netapp-files-metrics.md) 。 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>是否可以通过 Azure 存储资源管理器管理 Azure NetApp 文件？

不是。 Azure 存储资源管理器不支持 Azure NetApp 文件。

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>如何实现确定目录是否接近限制大小？

可以使用 `stat` 客户端中的命令查看目录是否接近目录元数据的最大大小限制 (320 MB) 。

对于 320 MB 的目录，块数为655360，每个块的大小为512个字节。   (，即 320x1024x1024/512 )   

示例：

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>数据迁移和保护常见问题解答

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>如何实现将数据迁移到 Azure NetApp 文件？
Azure NetApp 文件提供 NFS 和 SMB 卷。  你可以使用任何基于文件的复制工具将数据迁移到服务。 

NetApp 提供基于 SaaS 的解决方案，即 [Netapp 云同步](https://cloud.netapp.com/cloud-sync-service)。 解决方案使你能够将 NFS 或 SMB 数据复制到 Azure NetApp 文件 NFS 导出或 SMB 共享。 

你还可以使用广泛的免费工具来复制数据。 对于 NFS，可以使用工作负荷工具（如 [rsync](https://rsync.samba.org/examples.html) ）将源数据复制和同步到 Azure NetApp 文件量。 对于 SMB，可以采用相同的方式使用工作负荷 [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 。  这些工具还可以复制文件或文件夹的权限。 

从本地到 Azure NetApp 文件的数据迁移要求如下所示： 

- 确保 Azure NetApp 文件在目标 Azure 区域中可用。
- 验证源和 Azure NetApp 文件目标卷 IP 地址之间的网络连接。 本地和 Azure NetApp 文件服务之间的数据传输通过 ExpressRoute 支持。
- 创建目标 Azure NetApp 文件卷。
- 使用首选的文件复制工具将源数据传输到目标卷。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>如何实现在另一个 Azure 区域创建 Azure NetApp 文件卷的副本？
    
Azure NetApp 文件提供 NFS 和 SMB 卷。  可以使用任何基于文件的复制工具在 Azure 区域之间复制数据。 

NetApp 提供基于 SaaS 的解决方案，即 [Netapp 云同步](https://cloud.netapp.com/cloud-sync-service)。 解决方案使你能够将 NFS 或 SMB 数据复制到 Azure NetApp 文件 NFS 导出或 SMB 共享。 

你还可以使用广泛的免费工具来复制数据。 对于 NFS，可以使用工作负荷工具（如 [rsync](https://rsync.samba.org/examples.html) ）将源数据复制和同步到 Azure NetApp 文件量。 对于 SMB，可以采用相同的方式使用工作负荷 [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 。  这些工具还可以复制文件或文件夹的权限。 

将 Azure NetApp 文件卷复制到其他 Azure 区域的要求如下： 
- 确保 Azure NetApp 文件在目标 Azure 区域中可用。
- 验证每个区域中 Vnet 之间的网络连接。 目前，不支持 Vnet 之间的全局对等互连。  可以通过使用 ExpressRoute 线路或使用 S2S VPN 连接来建立 Vnet 之间的连接。 
- 创建目标 Azure NetApp 文件卷。
- 使用首选的文件复制工具将源数据传输到目标卷。

### <a name="is-migration-with-azure-data-box-supported"></a>Azure Data Box 是否支持迁移？

不是。 Azure Data Box 目前不支持 Azure NetApp 文件。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>是否支持通过 Azure 导入/导出服务进行迁移？

不是。 Azure 导入/导出服务目前不支持 Azure NetApp 文件。

## <a name="next-steps"></a>后续步骤  

- [Microsoft Azure ExpressRoute 常见问题](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure 虚拟网络常见问题](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox)
- [有关 Azure NetApp 文件的 SMB 性能的常见问题解答](azure-netapp-files-smb-performance.md)
