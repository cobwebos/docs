---
title: 通过 Azure PowerShell 在 Azure Stack Edge Pro GPU 设备上部署 Vm
description: 介绍如何使用 Azure PowerShell 在 Azure Stack Edge Pro GPU 设备上创建和管理虚拟机 (Vm) 。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: aa492acdedc2d131d28c894031de2181e87a2f3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890698"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>通过 Azure PowerShell 在 Azure Stack Edge Pro GPU 设备上部署 Vm

<!--[!INCLUDE [azure-stack-edge-gateway-deploy-vm-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]-->

本教程介绍如何使用 Azure PowerShell 在 Azure Stack Edge Pro 设备上创建和管理 VM。

## <a name="vm-deployment-workflow"></a>VM 部署工作流

下图演示了此部署工作流。

![VM 部署工作流](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

## <a name="prerequisites"></a>必备条件

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]



## <a name="query-for-built-in-subscription-on-the-device"></a>查询设备上的内置订阅

对于 Azure 资源管理器，仅支持单个用户可见的固定订阅。 此订阅对于每个设备都是唯一的，因此无法更改此订阅名称或订阅 ID。

此订阅包含创建 VM 所需的所有资源。 

> [!IMPORTANT]
> 此订阅未连接或与你的 Azure 订阅相关联，并在本地设备上生存。

此订阅将用于部署 Vm。

1.  若要列出此订阅，请键入：

    ```powershell
    Get-AzureRmSubscription
    ```
    
    下面显示了示例输出。

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
3.  获取在设备上运行的已注册资源提供程序的列表。 此列表通常包括计算、网络和存储。

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > 资源提供程序是预先注册的，不能修改或更改。
    
    下面显示了示例输出：

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源（例如存储帐户、磁盘、托管磁盘）的逻辑容器。

> [!IMPORTANT]
> 所有资源都在与设备相同的位置创建，并且位置设置为 **DBELocal**。

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

下面显示了示例输出。

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>创建存储帐户

使用在上一步中创建的资源组创建新的存储帐户。 这是一个 **本地存储帐户** ，将用于上载 VM 的虚拟磁盘映像。

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> 只能通过 Azure 资源管理器创建本地存储帐户，如本地冗余存储 (Standard_LRS 或 Premium_LRS) 。 若要创建分层存储帐户，请参阅 [添加、连接到 Azure Stack Edge Pro 上的存储帐户](azure-stack-edge-j-series-deploy-add-storage-accounts.md)中的步骤。

下面显示了示例输出。

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

若要获取存储帐户密钥，请运行 `Get-AzureRmStorageAccountKey` 命令。 此处显示了此命令的示例输出。

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-blob-uri-to-hosts-file"></a>将 blob URI 添加到 hosts 文件

已在 " [修改主机文件以进行终结点名称解析](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)" 部分中为用于连接到 blob 存储的客户端添加了 "主机" 文件中的 blob URI。 这是 blob URI 的条目：

\<Azure consistent network services VIP \>\<storage name\>blob。 \<appliance name\>\<dnsdomain\>


## <a name="install-certificates"></a>安装证书

如果使用的是 *https*，则需要在设备上安装适当的证书。 在这种情况下，请安装 blob 端点证书。 有关详细信息，请参阅如何在 " [管理证书](azure-stack-edge-j-series-manage-certificates.md)" 中创建和上载证书。

## <a name="upload-a-vhd"></a>上传 VHD

将所有要使用的磁盘映像复制到在前面步骤中创建的本地存储帐户中的页 blob 中。 可以使用 [AzCopy](../storage/common/storage-use-azcopy-v10.md) 等工具将 VHD 上传到在前面步骤中创建的存储帐户。 

使用 AzCopy 之前，请确保已 [正确配置 AzCopy](#configure-azcopy) ，以便与 Azure Stack Edge Pro 设备使用的 blob 存储 REST API 版本结合使用。

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> 设置 `BlobType` 为页面，用于从 VHD 创建托管磁盘。 `BlobType`当使用 AzCopy 写入分层存储帐户时，设置为 "阻止"。

你可以从 marketplace 下载磁盘映像。 有关详细步骤，请转到 [从 Azure Marketplace 获取虚拟磁盘映像](azure-stack-edge-j-series-create-virtual-machine-image.md)。

下面显示了使用 AzCopy 7.3 的示例输出。 有关此命令的详细信息，请参阅 [使用 AzCopy 将 VHD 文件上传到存储帐户](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md)。


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```


## <a name="create-managed-disks-from-the-vhd"></a>基于 VHD 创建托管磁盘

从上传的 VHD 创建托管磁盘。

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
下面显示了示例输出： 
<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

下面显示了示例输出。 有关此 cmdlet 的详细信息，请参阅 [get-azurermdisk](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0)。

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>基于映像托管磁盘创建 VM 映像

使用以下命令从托管磁盘创建 VM 映像。 将中的值替换 \< \> 为你选择的名称。

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

下面显示了示例输出。 有关此 cmdlet 的详细信息，请参阅 [get-azurermimage](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0)。

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-vm-with-previously-created-resources"></a>基于之前创建的资源创建 VM

创建和部署 VM 之前，必须创建一个虚拟网络并关联一个虚拟网络接口。

> [!IMPORTANT]
> 创建虚拟网络和虚拟网络接口时，以下规则适用：
> - 即使) 资源组，也只能创建一个 Vnet (，它必须与该地址空间中的逻辑网络完全匹配。
> -   Vnet 中只允许有一个子网。 子网必须与 Vnet 完全相同的地址空间。
> -   在创建 Vnic 的过程中，只允许使用静态分配方法，且用户需要提供专用 IP 地址。

 
**创建 Vnet**

```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```

**使用 Vnet 子网 ID 创建 Vnic**

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

这些命令的示例输出如下所示：

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

为 VM 创建 Vnic 时，可以选择传递公共 IP。 在此实例中，公共 IP 将返回专用 IP。 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```


**创建 VM**

你现在可以使用 VM 映像创建 VM，并将其附加到之前创建的虚拟网络。

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>连接到 VM

根据你创建的是 Windows 还是 Linux VM，连接步骤可能会有所不同。

### <a name="connect-to-linux-vm"></a>连接到 Linux VM

按照以下步骤连接到 Linux VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>连接到 Windows VM

按照以下步骤连接到 Windows VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command.

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this case will be the same as the private IP that you passed during virtual network interface creation.-->


## <a name="manage-vm"></a>管理 VM

以下部分介绍了将在 Azure Stack Edge Pro 设备上创建的 VM 的一些常见操作。

### <a name="list-vms-running-on-the-device"></a>列出在设备上运行的 Vm

若要返回 Azure Stack Edge Pro 设备上运行的所有 Vm 的列表，请运行以下命令。


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>打开 VM

运行以下 cmdlet 以打开在你的设备上运行的虚拟机：


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`


有关此 cmdlet 的详细信息，请参阅 [get-azurermvm](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0)。

### <a name="suspend-or-shut-down-the-vm"></a>挂起或关闭 VM

运行以下 cmdlet 以停止或关闭在你的设备上运行的虚拟机：


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```


有关此 cmdlet 的详细信息，请参阅 [get-azurermvm cmdlet](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0)。

### <a name="add-a-data-disk"></a>添加数据磁盘

如果 VM 上的工作负荷要求增加，则可能需要添加一个数据磁盘。

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>删除 VM

运行以下 cmdlet 以从设备中删除虚拟机：

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

有关此 cmdlet 的详细信息，请参阅 [get-azurermvm cmdlet](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0)。


## <a name="supported-vm-sizes"></a>支持的 VM 大小

VM 大小决定 VM 可用计算资源（如 CPU、GPU 和内存）的数量。 创建的虚拟机大小应适合工作负荷。 尽管所有计算机都将在同一硬件上运行，但计算机大小对磁盘访问具有不同的限制，这可以帮助你管理跨 Vm 的总体磁盘访问。 如果工作负荷增加，也可重设现有虚拟机的大小。

支持在 Azure Stack Edge Pro 设备上创建以下标准 Dv2 系列 Vm。

### <a name="dv2-series"></a>Dv2 系列
|大小     |vCPU     |内存 (GiB) | 临时存储 (GiB)  | 最大 OS 磁盘吞吐量 (IOPS) | 最大临时存储吞吐量 (IOPS) | 最大数据磁盘数/吞吐量 (IOPS) | 最大 NIC 数 |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3.5 |50   |500 |3000  |4/4x500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8/8x500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16/16x500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32/32x500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64/64x500 |8 |

### <a name="dsv2-series"></a>DSv2 系列
|大小     |vCPU     |内存 (GiB) | 临时存储 (GiB)  | 最大 OS 磁盘吞吐量 (IOPS) | 最大临时存储吞吐量 (IOPS) | 最大数据磁盘数/吞吐量 (IOPS) | 最大 NIC 数 |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3.5 |7   |1000 |4000  |4 / 4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16 / 16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64x2300 |8 |

### <a name="dv2-series"></a>Dv2 系列
|大小     |vCPU     |内存 (GiB) | 临时存储 (GiB)  | 最大 OS 磁盘吞吐量 (IOPS) | 最大临时存储吞吐量 (IOPS) | 最大数据磁盘数/吞吐量 (IOPS) | 最大 NIC 数 |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8/8x500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16/16x500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32/32x500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64/64x500  |8 |


### <a name="dsv2-series"></a>DSv2 系列
|大小     |vCPU     |内存 (GiB) | 临时存储 (GiB)  | 最大 OS 磁盘吞吐量 (IOPS) | 最大临时存储吞吐量 (IOPS) | 最大数据磁盘数/吞吐量 (IOPS) | 最大 NIC 数 |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4 / 4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8 / 8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16 / 16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32 / 32x2300  |8 |

有关详细信息，请参阅 [常规用途 VM 大小上的 Dv2 系列](../virtual-machines/dv2-dsv2-series.md#dv2-series)。

## <a name="unsupported-vm-operations-and-cmdlets"></a>不受支持的 VM 操作和 cmdlet

不支持扩展、规模集、可用性集和快照。

## <a name="configure-azcopy"></a>配置 AzCopy

安装最新版本的 AzCopy 时，需要配置 AzCopy，以确保它与 Azure Stack Edge Pro 设备的 blob 存储 REST API 版本相匹配。

在用于访问 Azure Stack Edge Pro 设备的客户端上，设置一个全局变量，使其与 blob 存储 REST API 版本相匹配。

### <a name="on-windows-client"></a>在 Windows 客户端上 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### <a name="on-linux-client"></a>在 Linux 客户端上

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

若要验证是否正确设置了 AzCopy 的环境变量，请执行以下步骤：

1. 运行 "azcopy env"
2. 查找 `AZCOPY_DEFAULT_SERVICE_API_VERSION` 参数。 这应具有你在前面的步骤中设置的值。


## <a name="next-steps"></a>后续步骤

[Azure 资源管理器 cmdlet](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
