---
title: 在 SQL Server 上使用 Azure 高级存储 | Microsoft Docs
description: 本文使用通过经典部署模型创建的资源并介绍如何对 Azure 虚拟机上运行的 SQL Server 使用 Azure 高级存储。
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: jroth
ms.openlocfilehash: 3d3fdd8865a293c5e2f0df6a97910ac8e2a07d4c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29400607"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>将 Azure 高级存储用于虚拟机上的 SQL Server
## <a name="overview"></a>概述
[Azure 高级存储](../premium-storage.md)是下一代提供低延迟和高吞吐量 IO 的存储。 它最适用于关键 IO 密集型工作负荷，例如 IaaS [虚拟机](https://azure.microsoft.com/services/virtual-machines/)上的 SQL Server。

> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[资源管理器和经典模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

本文提供迁移运行 SQL Server 的虚拟机以使用高级存储的规划和指南。 这包括 Azure 基础结构（网络、存储）以及来宾 Windows VM 步骤。 [附录](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)中的示例显示如何移动较大的 VM 以通过 PowerShell 利用改进的本地 SSD 存储的完整全面的端到端迁移。

请务必了解将 Azure 高级存储用于 IAAS VM 上的 SQL Server 的端到端过程。 这包括：

* 确定使用高级存储的先决条件。
* 作为新部署，将 IaaS 上的 SQL Server 部署到高级存储的示例。
* 迁移现有部署（包括独立服务器和使用 SQL Always On 可用性组的部署）的示例。
* 可能的迁移方法。
* 演示迁移现有 Always On 实现的 Azure、Windows 和 SQL Server 步骤的完整端到端示例。

有关 Azure 虚拟机中的 SQL Server 的更多背景信息，请参阅 [Azure 虚拟机中的 SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)。

**作者：** Daniel Sol **技术审阅人员：** Luis Carlos Vargas Herring、Sanjay Mishra、Pravin Mital、Juergen Thomas、Gonzalo Ruiz。

## <a name="prerequisites-for-premium-storage"></a>高级存储的先决条件
使用高级存储有多个先决条件。

### <a name="machine-size"></a>虚拟机大小
要使用高级存储，需要使用 DS 系列虚拟机 (VM)。 如果以前尚未在云服务中使用 DS 系列虚拟机，则必须在重新创建 VM 作为 DS* 角色大小之前，删除现有 VM、保留附加磁盘，并创建新的云服务。 有关虚拟机大小的详细信息，请参阅 [Azure 的虚拟机和云服务大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="cloud-services"></a>云服务
在新的云服务中创建 VM 时，只能将 DS* VM 用于高级存储。 如果在 Azure 中使用 SQL Server AlwaysOn，则 AlwaysOn 侦听器会引用与云服务关联的 Azure 内部或外部负载均衡器 IP 地址。 本文重点介绍如何在此方案中迁移，同时保持可用性。

> [!NOTE]
> DS* 系列必须是部署到新的云服务的第一个 VM。
>
>

### <a name="regional-vnets"></a>区域 VNET
对于 DS* VM，必须将托管 VM 的虚拟网络 (VNET) 配置为区域虚拟网络。 这会“加宽”虚拟网络，目的是允许在其他群集中设置更大的 VM，并允许这些 VM 之间进行通信。 在以下屏幕截图中，突出显示的位置显示的是区域 VNET，而第一个结果则显示的是“窄”VNET。

![RegionalVNET][1]

可提出要迁移到区域 VNET 的 Microsoft 支持票证。 随后，Microsoft 即会进行更改。 要完成到区域 VNET 的迁移，请更改网络配置中的 AffinityGroup 属性。 首先导出 PowerShell 中的网络配置，然后将 **VirtualNetworkSite** 元素中的 **AffinityGroup** 属性替换为 **Location** 属性。 指定 `Location = XXXX`，其中 `XXXX` 是 Azure 区域。 然后导入新配置。

例如，考虑以下 VNET 配置：

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

要将此项移到在欧洲西部的区域 VNET，请将配置更改为以下内容：

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>存储帐户
需要新建一个专为高级存储配置的存储帐户。 请注意，在存储帐户（而不是单个 VHD）上设置使用高级存储，但使用 DS* 系列 VM 时，则可以从高级和标准存储帐户附加 VHD。 如果不想将操作系统 VHD 放到高级存储帐户上，则可以考虑此项。

以下使用“Premium_LRS”**类型**的 **New-AzureStorageAccountPowerShell** 命令将创建高级存储帐户：

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>VHD 缓存设置
创建属于高级存储帐户的磁盘之间的主要区别是磁盘缓存设置。 对于 SQL Server 数据卷磁盘，建议使用“**读取缓存**”。 对于事务日志卷，磁盘缓存设置应设为“**无**”。 这不同于针对标准存储帐户的建议。

附加 VHD 后，便不能变更缓存设置。 将需要分离 VHD，再使用已更新的缓存设置重新附加该 VHD。

### <a name="windows-storage-spaces"></a>Windows 存储空间
可如在之前的标准存储操作一样使用 [Windows 存储空间](https://technet.microsoft.com/library/hh831739.aspx)，这样即可对当前使用存储空间的 VM 进行迁移。 [附录](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)中的示例（步骤 9 及前面的步骤）演示了提取并导入附加了多个 VHD 的 VM 的 Powershell 代码。

存储池已用于标准 Azure 存储帐户以提高吞吐量并减少延迟。 在使用新部署的高级存储测试存储池时，你可能会发现价值，但这样做会为存储设置添加额外的复杂性。

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>如何查明哪些 Azure 虚拟磁盘映射到存储池
因为针对附加 VHD 有不同的缓存设置建议，你可能会决定将 VHD 复制到高级存储帐户。 但是，将它们重新附加到新的 DS 系列 VM 时，可能需要变更缓存设置。 对 SQL 数据文件和日志文件使用单独的 VHD（而不是同时包含这两种文件的单个 VHD）时，应用高级存储建议的缓存设置将更为简单。

> [!NOTE]
> 如果在同一卷上有 SQL Server 数据和日志文件，则所选的缓存选项将取决于数据库工作负荷的 IO 访问模式。 只有测试可演示哪个缓存选项最适用于这种情况。
>
>

但是，如果使用的 Windows 存储空间由多个 VHD 构成，则需要查看原始脚本，确认每个附加的 VHD 位于哪些特定池中，然后才可相应地对每个磁盘设定缓存设置。

如果没有可向你显示哪些 VHD 映射到存储池的原始脚本，则可以使用以下步骤来确定磁盘/存储池映射。

对于每个磁盘，使用以下步骤：

1. 使用 **Get-AzureVM** 命令获取附加到 VM 的磁盘列表：

    Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
2. 记下 Diskname 和 LUN。

    ![DisknameAndLUN][2]
3. 通过远程桌面连接到 VM。 然后依次转到“计算机管理” | “设备管理器” | “磁盘驱动器”。 查看每个 Microsoft 虚拟磁盘的属性

    ![VirtualDiskProperties][3]
4. 此处的 LUN 号是对你在将 VHD 附加到 VM 时指定的 LUN 号的引用。
5. 对于“Microsoft 虚拟磁盘”，转到“详细信息”选项卡，并在“属性”列表中转到“驱动程序键”。 在“值”中，注意“偏移量”，该项在下面的屏幕截图中为 0002。 0002 表示存储池引用的 PhysicalDisk2。

    ![VirtualDiskPropertyDetails][4]
6. 对于每个存储池，转储关联的磁盘：

    Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]

现在，可以使用此信息将附加 VHD 关联到存储池中的物理磁盘。

将 VHD 映射到存储池中的物理磁盘后，可以将其分离并复制到高级存储帐户，并使用正确的缓存设置附加它们。 请参阅[附录](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)步骤 8 至 12 中的示例。 以下步骤显示如何将 VM 附加的 VHD 磁盘配置提取到 CSV 文件、复制 VHD、变更磁盘配置缓存设置，最后将 VM 重新部署为带所有附加磁盘的 DS 系列 VM。

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM 存储带宽和 VHD 存储吞吐量
存储量性能取决于指定的 DS* VM 大小和 VHD 大小。 可附加到 VM 的 VHD 限额数量不同，且每个 VM 支持的最大带宽（MB/秒）也不同。 有关特定带宽数字，请参阅 [Azure 的虚拟机和云服务大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

较大的磁盘大小可提高 IOPS。 考虑迁移路径时，应考虑这一点。 有关详细信息，[请参阅 IOPS 和磁盘类型的表](../premium-storage.md#scalability-and-performance-targets)。

最后，需考虑到对于所附加的每个磁盘，VM 支持的最大磁盘带宽不同。 在高负载下可使可供该 VM 角色大小使用的最大磁盘带宽饱和。 例如，Standard_DS14 最多支持 512MB/秒；因此，三个 P30 磁盘就能让 VM 的磁盘带宽达到饱和。 但在此示例中，可以超出吞吐量限制，具体取决于读取和写入 IO 的组合。

## <a name="new-deployments"></a>新建部署
接下来的两部分将演示如何将 SQL Server VM 部署到高级存储。 如前所述，不一定需要将操作系统磁盘放到高级存储上。 如果打算将任何密集型 IO 工作负荷放置在操作系统 VHD 上，则可以选择这样做。

第一个示例演示如何利用现有 Azure 库映像。 第二个示例演示如何使用在现有标准存储帐户中拥有的自定义 VM 映像。

> [!NOTE]
> 这些示例假定已创建区域 VNET。
>
>

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>使用库映像创建带高级存储的新 VM
下面的示例演示如何将操作系统 VHD 放置到高级存储上并附加高级存储 VHD。 但是，也可以将操作系统磁盘放置在标准存储帐户中，并附加驻留在高级存储帐户中的 VHD。 将演示这两种方案。

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>步骤 1：创建高级存储帐户
    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>步骤 2：创建新的云服务
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>步骤 3：保留云服务 VIP（可选）
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>步骤 4：创建 VM 容器
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>步骤 5：将操作系统 VHD 放置在标准或高级存储上
    #NOTE: Set up subscription and default storage account which is used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>步骤 6：创建 VM
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>使用自定义映像创建新的 VM 以使用高级存储
此方案演示拥有驻留在标准存储帐户中的现有自定义映像的位置。 如前所述，如果要将操作系统 VHD 放置在高级存储上，需要复制标准存储帐户中存在的映像，并将它们传输到高级存储中，才能使用它们。 如果在本地有一个映像，也可以使用此方法将该映像直接复制到高级存储帐户。

#### <a name="step-1-create-storage-account"></a>步骤 1：创建存储帐户
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>步骤 2：创建云服务
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>步骤 3：使用现有映像
可以使用现有映像， 也可以[创建现有虚拟机的映像](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 请注意，执行映像的计算器不一定要是 DS* 计算机。 获得映像后，以下步骤演示如何使用 **Start-AzureStorageBlobCopy** PowerShell commandlet 将其复制到高级存储帐户。

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>步骤 4：在存储帐户之间复制 Blob
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>步骤 5：定期检查复制状态：
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>步骤 6：在订阅中将映像磁盘添加到 Azure 磁盘存储库
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [!NOTE]
> 你可能会发现即使状态报告为成功，也仍会收到磁盘租约错误。 在这种情况下，请等待大约 10 分钟。
>
>

#### <a name="step-7--build-the-vm"></a>步骤 7：生成 VM
在此处可基于映像生成 VM 并附加两个高级存储 VHD：

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This needs to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>未使用 Always On 可用性组的现有部署
> [!NOTE]
> 对于现有部署，请先参阅本文的[先决条件](#prerequisites-for-premium-storage)部分。
>
>

未使用 Always On 可用性组的 SQL Server 部署和使用这些组的 SQL Server 部署有不同的注意事项。 如果未使用 Always On 并且有现有的独立 SQL Server，则可以通过使用新的云服务和存储帐户升级到高级存储。 请考虑以下选项：

* **创建新的 SQL Server VM**。 可以创建使用高级存储帐户的新 SQL Server VM，如“新建部署”中所述。 然后对 SQL Server 和用户数据库进行备份和还原。 无论是从内部还是外部进行访问，都需要更新应用程序才可引用新的 SQL Server 。 需要复制所有“数据库外”对象，就像执行并排 (SxS) SQL Server 迁移一样。 这包括登录名、证书和链接服务器等对象。
* **迁移现有 SQL Server VM**。 为此，需使 SQL Server VM 脱机，然后将其传输到新的云服务，包括将其附加的所有 VHD 复制到高级存储帐户。 VM 重新联机后，应用程序将如之前一般引用服务器主机名。 请注意，现有磁盘的大小会映像性能特征。 例如，400 GB 磁盘将向上舍入到 P20。 如果知道不需要该磁盘性能，则可以将 VM 重新创建为 DS 系列 VM，并附加具有所需大小/性能指标的高级存储 VHD。 然后，可以分离并重新附加 SQL DB 文件。

> [!NOTE]
> 复制 VHD 磁盘时，应注意磁盘大小。取决于大小是指这些磁盘所属的高级存储磁盘类型，它决定了磁盘性能规格。 Azure 向上四舍五入到最近的磁盘大小，因此如果你的磁盘为 400GB，系统将舍入为 P20。 根据操作系统 VHD 的现有 IO 要求，可能不需要将此 VHD 迁移到高级存储帐户。
>
>

如果是从外部访问 SQL Server，则云服务 VIP 将不同。 还必须更新终结点、ACL 和 DNS 设置。

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>使用 Always On 可用性组的现有部署
> [!NOTE]
> 对于现有部署，请先参阅本文的[先决条件](#prerequisites-for-premium-storage)部分。
>
>

在本部分的开头，将介绍 AlwaysOn 如何与 Azure 网络进行交互。 然后将迁移细分为两个部分：可容忍某些停机情况的迁移，以及必须达到最低停机时间的迁移。

本地 SQL Server Always On 可用性组在本地使用这样的侦听器：它注册一个虚拟 DNS 名称，以及在一个或多个 SQL Server 之间共享的一个 IP 地址。 当客户端连接时，会将它们通过侦听器 IP 路由到主 SQL Server。 这是在该时间拥有 Always On IP 资源的服务器。

![DeploymentsUseAlways On][6]

在 Microsoft Azure 中，只能将一个 IP 地址分配给 VM 上的 NIC，因此，为了实现与本地相同的抽象层，Azure 将利用分配给内部/外部负载均衡器 (ILB/ELB) 的 IP 地址。 在服务器间共享的 IP 资源将设置为与 ILB/ELB 相同的 IP。 此 IP 在 DNS 中发布，客户端流量将通过 ILB/ELB 传递到主 SQL Server 副本。 ILB/ELB 知道哪个 SQL Server 为主，因为它使用探测器来探测 Always On IP 资源。 在前面的示例中，它会探测包含 ELB/ILB 引用的终结点的每个节点，做出响应的则是主 SQL Server。

> [!NOTE]
> ILB 和 ELB 均必须分配给特定的 Azure 云服务，因此最可能出现的情况是 Azure 中的云迁移均会造成负载均衡器 IP 发生改变。
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>迁移可以允许停机一段时间的 Always On 部署
有两种策略可以迁移允许停机一段时间的 Always On 部署：

1. **将更多辅助副本添加到现有 Always On 群集**
2. **迁移到新的 Always On 群集**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1.将更多辅助副本添加到现有 Always On 群集
一种策略是将更多辅助副本添加到 Always On 可用性组。 需要将这些辅助副本添加到新的云服务中，并使用新的负载均衡器 IP 更新侦听器。

##### <a name="points-of-downtime"></a>停机时间点：
* 群集验证。
* 对新的辅助副本测试 Always On 故障转移。

如果在 VM 中使用 Windows 存储池来提高 IO 吞吐量，则这些副本在完全群集验证期间处于脱机状态。 向群集添加节点时需要进行验证测试。 运行测试所花的时间不同，因此需在各自的测试环境中进行测试，了解该操作所耗费的大致时间。

应设置可以对新添加的节点执行手动故障转移和混沌测试的时间，以确保 Always On 高可用性功能正常工作。

![DeploymentUseAlways On2][7]

> [!NOTE]
> 运行验证前，应停止使用存储池的 SQL Server 的所有实例。
>
> ##### <a name="high-level-steps"></a>高级步骤
>

1. 在使用附加高级存储的新云服务中创建两个新的 SQL Server。
2. 使用 **NORECOVERY** 复制完整备份并进行还原。
3. 复制“用户数据库外”依赖对象，例如登录名等。
4. 新建内部负载均衡器 (ILB) 或使用外部负载均衡器 (ELB)，并在这两个新节点上设置负载均衡终结点。

   > [!NOTE]
   > 继续下一步之前，检查所有节点的终结点配置是否正确
   >
   >
5. 禁止用户/应用程序访问 SQL Server（如果使用存储池）。
6. 停止所有节点上的 SQL Server 引擎服务（如果使用存储池）。
7. 将新节点添加到群集并运行完整验证。
8. 成功验证后，启动所有 SQL Server 服务。
9. 备份事务日志并还原用户数据库。
10. 将新节点添加到 Always On 可用性组中，并将复制放置到“同步”中。
11. 根据[附录](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)中的多站点示例，通过 PowerShell 为 Always On 添加新云服务 ILB/ELB 的 IP 地址资源。 在 Windows 群集中，将 **IP 地址**资源的**可能所有者**设置为新节点 old。 请参阅[附录](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)的“在同一子网中添加 IP 地址资源”部分。
12. 故障转移到新节点之一。
13. 将新节点设为“自动故障转移伙伴”并测试故障转移。
14. 从可用性组中删除原始节点。

##### <a name="advantages"></a>优点
* 在将新 SQL Server（SQL Server 和应用程序）添加到 Always On 之前，可以对其进行测试。
* 可以根据确切需求更改 VM 大小和自定义存储。 但是，保持所有 SQL 文件路径不变会非常有益。
* 可控制何时开始将 DB 备份转移到辅助副本。 这不同于使用 Azure **Start-AzureStorageBlobCopy** commandlet 复制 VHD，因为这是异步复制。

##### <a name="disadvantages"></a>缺点
* 使用 Windows 存储池时，在对新的附加节点进行完整群集验证的过程中，会存在群集停机时间。
* 根据 SQL Server 版本和现有的辅助副本数，在不删除现有辅助副本的情况下，可能不能添加更多辅助副本。
* 设置辅助副本时，SQL 数据传输时间可能会很长。
* 在迁移期间，并行运行新计算机时，会产生额外的费用。

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2.迁移到新的 Always On 群集
另一种策略是在新的云服务中创建包含全新节点的全新 Always On 群集，并重定向客户端以使用该群集。

##### <a name="points-of-downtime"></a>停机时间点
将应用程序和用户转移到新的 Always On 侦听器时，会出现停机时间。 停机时间取决于：

* 将最后一个事务日志备份还原到新服务器上的数据库时所用的时间。
* 更新客户端应用程序以使用新的 Always On 侦听器时所用的时间。

##### <a name="advantages"></a>优点
* 可以测试实际生产环境、SQL Server 和操作系统生成更改。
* 可以选择自定义存储并可能减小 VM 的大小。 这可以导致成本降低。
* 可以在此过程中更新 SQL Server 版本。 还可以升级操作系统。
* 以前的 Always On 群集可以充当可靠的回滚目标。

##### <a name="disadvantages"></a>缺点
* 如果希望同时运行两个 Always On 群集，则需要更改侦听器的 DNS 名称。 这会增加迁移过程中的管理开销，因为客户端应用程序字符串必须反映新的侦听器名称。
* 必须实现两种环境之间的同步机制，使它们尽可能接近，以最大程度地降低在迁移之前执行最终同步的要求。
* 在迁移期间运行新环境会增加成本。

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>迁移 Always On 部署以实现最短停机时间
有两种迁移 Always On 部署的策略可实现最短停机时间：

1. **利用现有的辅助副本：单站点**
2. **利用现有的辅助副本：多站点**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1.利用现有的辅助副本：单站点
停机时间最短的一个策略是获取现有的云辅助版本并将其从当前云服务中删除。 然后将 VHD 复制到新的高级存储帐户，并在新的云服务中创建 VM。 然后，在群集和故障转移中更新侦听器。

##### <a name="points-of-downtime"></a>停机时间点
* 使用负载均衡终结点更新最后一个节点时，会出现停机时间。
* 客户端重新连接可能会延迟，具体取决于客户端/DNS 配置。
* 如果选择将 Always On 群集组脱机来换出 IP 地址，则会增加停机时间。 可以通过对添加的 IP 地址资源使用 OR 依赖关系和可能的所有者来避免出现这种情况。 请参阅[附录](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)的“在同一子网中添加 IP 地址资源”部分。

> [!NOTE]
> 如果要让添加的节点作为 Always On 故障转移伙伴参与其中，则需要添加带有对负载均衡集的引用的 Azure 终结点。 运行 Add-AzureEndpoint 命令来执行此操作时，当前连接保持断开，但在更新负载均衡器之后，才能建立到侦听器的新连接。 在测试时，看到此现像持续 90 到 120 秒，应该对此进行测试。
>
>

##### <a name="advantages"></a>优点
* 在迁移期间不会产生额外的费用。
* 一对一迁移。
* 降低了复杂性。
* 可提高高级存储 SKU 的 IOPS。 将磁盘与 VM 分离并复制到新的云服务中时，可以使用第三方工具来增加 VHD 大小，以便提供更高的吞吐量。 有关增加 VHD 大小的信息，请参阅此[论坛讨论](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows)。

##### <a name="disadvantages"></a>缺点
* 在迁移期间，会暂时失去高可用性和灾难恢复。
* 由于此操作是一对一迁移，因此必须使用支持 VHD 数量的最小 VM 大小，这样可能就无法缩小 VM 的大小。
* 此方案将使用 Azure **Start-AzureStorageBlobCopy** commandlet，它是异步的。 复制完成后没有 SLA。 复制所用的时间不同，而这取决于在队列中的等待情况，还取决于要传输的数据量。 如果传输转到另一个在其他区域中支持高级存储的 Azure 数据中心，则复制时间将增加。 如果只有 2 个节点，请考虑在复制所用时间长于测试时的可能的缓解措施。 这可以包括以下建议。
  * 在使用商定的停机时间进行迁移之前，添加临时的第 3 个 SQL Server 节点以实现 HA。
  * 在 Azure 计划的维护之外运行迁移。
  * 确保已正确配置群集仲裁。  

##### <a name="high-level-steps"></a>高级步骤
本文档并不演示完整的端到端示例，但是[附录](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)提供了可用来执行此操作的详细信息。

![MinimalDowntime][8]

* 收集磁盘配置并删除节点（不删除附加的 VHD）。
* 创建高级存储帐户并从标准存储帐户复制 VHD
* 创建新的云服务并在该云服务中重新部署 SQL2 VM。 使用复制的原始操作系统 VHD 创建 VM 并附加复制的 VHD。
* 配置 ILB/ELB 并添加终结点。
* 通过以下任一方法更新侦听器：
  * 将 Always On 组脱机并使用新的 ILB/ELB IP 地址更新 Always On 侦听器。
  * 或者通过 PowerShell 将新云服务 ILB/ELB 的 IP 地址资源添加到 Windows 群集。 然后，将 IP 地址资源的可能所有者设置为已迁移节点 SQL2，并在网络名称中将此项设置为 OR 依赖关系。 请参阅[附录](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)的“在同一子网中添加 IP 地址资源”部分。
* 检查客户端的 DNS 配置/传播。
* 迁移 SQL1 VM，并完成步骤 2-4。
* 如果使用步骤 5ii，则将 SQL1 添加为已添加的 IP 地址资源的可能所有者
* 测试故障转移。

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2.利用现有的辅助副本：多站点
如果在多个 Azure 数据中心 (DC) 中有节点，或者拥有混合环境，则可以在此环境中使用 Always On 配置以最大程度减少停机时间。

此方法的目的是将本地或辅助 Azure DC 的 Always On 同步更改为同步，并故障转移到该 SQL Server。 然后将 VHD 复制到一个高级存储帐户，并将计算机重新部署到新的云服务中。 更新侦听器，并故障恢复。

##### <a name="points-of-downtime"></a>停机时间点
停机时间包含故障转移到备用 DC 并返回的时间。 它还取决于客户端/DNS 配置，并且客户端重新连接可能会延迟。
请考虑以下混合 Always On 配置示例：

![MultiSite1][9]

##### <a name="advantages"></a>优点
* 可以利用现有基础结构。
* 可以选择先在 DR Azure DC 上预升级 Azure 存储。
* 可以重新配置 DR Azure DC 存储。
* 在迁移期间，至少进行两次故障转移，不包括测试故障转移。
* 无法使用备份和还原即可移动 SQL Server 数据。

##### <a name="disadvantages"></a>缺点
* 根据客户端对 SQL Server 的访问权限，当 SQL Server 在应用程序的备用 DC 中运行时，延迟时间可能会增加。
* VHD 到高级存储的复制时间可能会很长。 这可能会影响是否要在可用性组中保留节点的决策。 请在确定何时需要在迁移期间运行日志密集型工作负荷时考虑这一点，因为主节点必须将未复制的事务保留在事务日志中。 因此，此日志可能会显著增长。
* 此方案将使用 Azure **Start-AzureStorageBlobCopy** commandlet，它是异步的。 完成后没有 SLA。 复制所用的时间不同，而这取决于在队列中的等待情况，还取决于要传输的数据量。 因此，在第 2 个数据中心中只有一个节点，在复制所用时间长于测试的情况下，应该采取缓解措施。 这些迁移步骤包括以下方面：
  * 在使用商定的停机时间进行迁移之前，添加临时的第 2 个 SQL 节点以实现 HA。
  * 在 Azure 计划的维护之外运行迁移。
  * 确保已正确配置群集仲裁。

此方案假定已记录安装，并知道如何存储映射以便对最佳磁盘缓存设置进行更改。

##### <a name="high-level-steps"></a>高级步骤
![Multisite2][10]

* 将本地/备用 Azure DC 设为主 SQL Server，并将设为其他自动故障转移伙伴 (AFP)。
* 从 SQL2 收集磁盘配置信息并删除该节点（不删除附加的 VHD）。
* 创建高级存储帐户并从标准存储帐户复制 VHD。
* 创建新的云服务，并使用其附加高级存储磁盘创建 SQL2 VM。
* 配置 ILB/ELB 并添加终结点。
* 使用新的 ILB/ELB IP 地址更新 Always On 侦听器并测试故障转移。
* 检查 DNS 配置。
* 将 AFP 更改为 SQL2，迁移 SQL1 并完成步骤 2 - 5。
* 测试故障转移。
* 将 AFP 切换回 SQL1 和 SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>附录：将多站点 Always On 群集迁移到高级存储
本文的剩余部分介绍了一个将多站点 AlwaysOn 群集转换为高级存储的详细示例。 它还将侦听器从使用外部负载均衡器 (ELB) 转换为使用内部负载均衡器 (ILB)。

### <a name="environment"></a>环境
* Windows 2k12/SQL 2k12
* SP 上的 1 个 DB 文件
* 2 x 每个节点的存储池

![Appendix1][11]

### <a name="vm"></a>VM：
本例中，将展示如何从 ELB 迁移到 ILB。 在 ILB 之前推出的是 ELB，因此本例展示如何在迁移期间切换到 ILB。

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>预先步骤：连接到订阅
    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>步骤 1：创建新的存储帐户和云服务
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>步骤 2：在资源上增加允许的故障 <Optional>
在 AlwaysOn 可用性组中包含的某些资源上，限定了在群集服务尝试重启资源组的固定时间内可出现的失败数。 在完成此过程时，建议增加此限制，因为如果未手动故障转移或通过关闭计算机来触发故障转移，则可能会接近此限制。

明智的做法是，使允许的故障数加倍，为此，请在故障转移群集管理器中，转到 Always On 资源组的属性：

![Appendix3][13]

将最大故障数更改为 6。

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>步骤 3：为群集组添加 IP 地址资源 <Optional>
如果群集组只有一个 IP 地址，而此地址分配给了云子网，则请注意，如果意外地使此玩过上云端的所有群集脱机，群集 IP 资源和群集网络名称将无法再联机。 此情况下，这会阻止更新到其他群集资源。

#### <a name="step-4-dns-configuration"></a>步骤 4：DNS 配置
转移是否顺畅取决于如何利用和更新 DNS。
安装 AlwaysOn 时，会创建一个 Windows 群集资源组；如果打开群集资源管理器，会看到最低程度时具有三个资源，而文档所引用的两个资源为：

* 虚拟网络名称 (VNN) - 想要通过 AlwaysOn 连接到 SQL Server 时客户端连接到的 DNS 名称。
* IP 地址资源 - 与 VNN 关联的 IP 地址，可具有多个 IP 地址，且在多站点配置中，每个站点/子网均具有一个 IP 地址。

连接到 SQL Server 时，SQL Server 客户端驱动程序会检索与侦听器关联的 DNS 记录，并尝试连接到每个关联到 AlwaysOn 的 IP 地址。 接下来，将讨论会影响此进程的一些因素。

与侦听器名称关联的并发 DNS 记录数不仅取决于关联的 IP 地址数，而且还取决于 Always ON VNN 资源的故障转移群集的“RegisterAllIpProviders”设置。

在 Azure 中部署 Always On 时，可使用不同的步骤创建侦听器和 IP 地址，必须手动将“RegisterAllIpProviders”配置为 1，这与本地 Always On 部署不同，后者已设置为 1。

如果“RegisterAllIpProviders”为零，则与侦听器关联的 DNS 中仅显示一条 DNS 记录：

![Appendix4][14]

如果“RegisterAllIpProviders”为 1：

![Appendix5][15]

以下代码会转储 VNN 设置并对其进行设置。 需要使 VNN 脱机，再将其还原到联机状态，才能使更改生效。 这会使侦听器脱机，进而导致客户端连接中断。

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

在之后的迁移步骤中，需要使用引用负载均衡器的更新后的 IP 地址来更新 AlwaysOn 侦听器，此操作涉及到删除和添加 IP 地址资源。 更新 IP 之后，需要确保已在 DNS 区域中更新新的 IP 地址并且客户端将更新其本地 DNS 缓存。

如果你的客户端驻留在不同的网络段中且引用不同的 DNS 服务器，则需要考虑到迁移期间 DNS 区域复制发生的情况，因为侦听器的任意新 IP 地址的区域复制时间都会制约应用程序连接时间（可能还有其他制约情况）。 如果在此处受到时间约束，则应与 Windows 团队讨论并测试强制增量区域传送，同时还应将 DNS 主机记录设为较小的生存时间 (TTL)，以使客户端更新。 有关详细信息，请参阅[增量区域传送](https://technet.microsoft.com/library/cc958973.aspx)和 [Start-DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx)。

默认情况下，与 Azure 中的 Always On 中的侦听器关联的 DNS 记录的 TTL 为 1200 秒。 如果在迁移期间受时间约束，你可能希望减少此时间，以确保客户端使用侦听器更新后的 IP 地址更新其 DNS。 可以通过转储 VNN 的配置来查看并修改该配置：

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

> [!NOTE]
> “HostRecordTTL”越低，DNS 流量越大。

##### <a name="client-application-settings"></a>客户端应用程序设置
如果你的 SQL 客户端应用程序支持 .Net 4.5 SQLClient，则可使用“MULTISUBNETFAILOVER=TRUE”关键字。 必须使用此关键字，因为它可加快故障转移期间到 SQL AlwaysOn 可用性组的连接速度。 它可以在故障转移过程中枚举与 Always On 侦听器并行关联的所有 IP 地址，并执行更激进的 TCP 连接重试速度。

有关先前设置的详细信息，请参阅[MultiSubnetFailover 关键字及关联的功能](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)。 另请参阅 [SqlClient 对高可用性、灾难恢复的支持](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx)。

#### <a name="step-5-cluster-quorum-settings"></a>步骤 5：群集仲裁设置
由于你计划一次至少关闭一个 SQL Server，因此必须修改群集仲裁设置，且如果使用带两个节点的文件共享见证 (FSW)，也必须设置仲裁来实现节点多数原则和进行动态投票，这样即可使单个节点保持运行。

    Set-ClusterQuorum -NodeMajority  

若要深入了解如何管理和配置群集仲裁，请参阅[在 Windows Server 2012 故障转移群集中配置和管理仲裁](https://technet.microsoft.com/library/jj612870.aspx)。

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>步骤 6：提取现有终结点和 ACL
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

将此文本保存到文件中。

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>步骤 7：更改故障转移伙伴和复制模式
如果具有两个以上的 SQL Server，则必须将另一 DC 或本地项中另一辅助节点的故障转移设置为“同步”，并将其设置为自动故障转移伙伴 (AFP)，这样即可在进行更改时保证高可用性。 可以通过修改 TSQL 来执行此操作（虽然 SSMS 也可以）：

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>步骤 8：从云服务中删除辅助 VM
必须先计划迁移云辅助节点。 如果此节点当前为主节点，则必须手动进行故障转移。

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>步骤 9：更改 CSV 文件中的磁盘缓存设置并保存
对于数据卷，必须将这些项设置为“只读”。

对于 TLOG 卷，必须将其设置为“无”。

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>步骤 10：复制 VHD
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



可以检查高级存储帐户的 VHD 的复制状态：

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

等到所有这些状态都记录为成功。

如需单个 blob 的信息：

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>步骤 11：注册 OS 磁盘
    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>步骤 12：将辅助副本导入到新的云服务
以下代码还使用此处添加的选项，可以导入计算机，并使用 可保留的 VIP。

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>步骤 13：在新的云服务上创建 ILB，添加负载均衡终结点和 ACL
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

#### <a name="step-14-update-always-on"></a>步骤 14：更新 Always On
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

现在，删除旧的云服务 IP 地址。

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>步骤 15：DNS 更新检查
现在，应检查 SQL Server 客户端网络上的 DNS 服务器，并确保群集已为添加的 IP 地址添加额外的主机记录。 如果这些 DNS 服务器未更新，请考虑强制进行 DNS 区域传送并确保该子网中的客户端能够解析为这两个 Always On IP 地址，以便无需等待自动 DNS 复制。

#### <a name="step-16-reconfigure-always-on"></a>步骤 16：重新配置 Always On
此时，需在已迁移的辅助节点与本地节点完全同步之前稍作等待，然后切换到同步复制节点并将其设置为 AFP。  

#### <a name="step-17-migrate-second-node"></a>步骤 17：迁移辅助节点
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>步骤 18：更改 CSV 文件中的磁盘缓存设置并保存
对于数据卷，应将缓存设置设为“只读”。

对于 TLOG 卷，应将缓存设置设为“无”。

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>步骤 19：为辅助节点创建新的独立存储帐户
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>步骤 20：复制 VHD
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


可以检查所有 VHD 的 VHD 复制状态：ForEach ($disk in $diskobjects) { $lun = $disk.Lun $vhdname = $disk.vhdname $cacheoption = $disk.HostCaching $disklabel = $disk.DiskLabel $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

等到所有这些状态都记录为成功。

如需单个 blob 的信息：

    #Check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### <a name="step-21-register-os-disk"></a>步骤 21：注册 OS 磁盘
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>步骤 22：添加负载均衡终结点和 ACL
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>步骤 23：测试故障转移
请在已迁移的节点与本地 AlwaysOn 节点同步之前稍作等待。 将其置于同步复制模式中，并在同步完成前稍作等待。 然后从本地故障转移到第一个已迁移的节点（即 AFP）。 故障转移完成后，将最后一个已迁移的节点更改为 AFP。

应测试所有节点之间的故障转移，并运行所有混沌测试，以确保故障转移及时地正常工作。

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>步骤 24：置回群集仲裁设置/DNS TTL/故障转移伙伴/同步设置
##### <a name="adding-ip-address-resource-on-same-subnet"></a>在同一子网上添加 IP 地址资源
如果你只有两个 SQL Server，你想将它们迁移到新的云服务但希望两者位于同一子网上，可让侦听器继续保持联机，以删除原始 AlwaysOn IP 地址并添加新的 IP 地址。 如果要将 VM 迁移到其他子网，则无需执行此操作，因为还存在一个引用该子网的群集网络。

启动已迁移的辅助节点并在故障转移现有的主节点之前将其添加到新云服务的新 IP 地址资源中后，应在群集故障转移管理器执行以下步骤：

若要添加 IP 地址，请参阅[附录](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)步骤 14。

1. 对于当前 IP 地址资源，将可能具有的所有者更改为“Existing Primary SQL Server”（在本例中为“dansqlams4”）：

    ![Appendix13][23]
2. 对于新的 IP 地址资源，将可能具有的所有者更改为“Migrated secondary SQL Server”（在本例中为“dansqlams5”）：

    ![Appendix14][24]
3. 设置了此项后便可以进行故障转移了，迁移了最后一个节点后，应编辑“可能的所有者”，以便将该节点添加为可能的所有者：

    ![Appendix15][25]

## <a name="additional-resources"></a>其他资源
* [Azure 高级存储](../premium-storage.md)
* [虚拟机](https://azure.microsoft.com/services/virtual-machines/)
* [Azure 虚拟机中的 SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
