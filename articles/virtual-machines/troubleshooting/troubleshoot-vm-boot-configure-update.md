---
title: VM 启动时停滞，并在 Azure 中显示“正在准备 Windows。 请不要关闭计算机”| Microsoft Docs
description: 介绍了解决以下问题的步骤：VM 启动时停滞并显示“正在准备 Windows。 请不要关闭计算机。”
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 2f3c18ea1887ea5b05bb89f85371139ac83dfe49
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080164"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM 启动时停滞，并在 Azure 中显示“正在准备 Windows。 请不要关闭计算机”

本文可帮助解决虚拟机 (VM) 在启动时停滞，并显示“正在准备 Windows。 请不要关闭计算机”的问题。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>症状

当使用“启动诊断”获取 VM 的屏幕截图时，操作系统无法完全启动。 VM 显示消息“正在准备 Windows。 请不要关闭计算机。”

![Windows Server 2012 R2 的消息示例](./media/troubleshoot-vm-configure-update-boot/message1.png)

![消息示例](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>原因

当服务器在配置发生更改后执行最终重新启动时，通常会发生此问题。 配置更改可能通过 Windows 更新或通过更改服务器的角色/功能进行初始化。 对于 Windows 更新，如果更新的大小较大，则操作系统将需要更多时间来重新配置更改。

## <a name="back-up-the-os-disk"></a>备份 OS 磁盘

在尝试解决该问题之前，请备份 OS 磁盘。

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>对于具有加密的磁盘的 VM，必须先对磁盘进行解锁

请按照下列步骤确定 VM 是否为已加密 VM。

1. 在 Azure 门户中，打开 VM，然后浏览到磁盘。

2. 查看“加密”列，查看是否启用了加密。

如果 OS 磁盘已加密，请解锁加密的磁盘。 要解锁磁盘，请按照下列步骤操作。

1. 创建与受影响的 VM 位于相同的资源组、存储帐户和位置的一个恢复 VM。

2. 在 Azure 门户中，删除受影响的 VM 并保留磁盘。

3. 以管理员身份运行 PowerShell。

4. 运行以下 cmdlet 来获取机密名称。

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. 获得机密名称后，在 PowerShell 中运行以下命令。

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. 将 Base64 编码的值转换为字节，并将输出写入到一个文件。 

    > [!Note]
    > 如果使用 USB 解锁选项，则 BEK 文件名必须与原始 BEK GUID 匹配。 在执行这些步骤之前，请在 C 驱动器上创建一个名为“BEK”的文件夹。
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. 在电脑上创建 BEK 文件后，将该文件复制到你已将锁定的 OS 磁盘附加到的恢复 VM。 通过使用 BEK 文件位置来运行以下命令。

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    可选：在某些情况下，可能需要通过使用以下命令来解密该磁盘。
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > 上一个命令假定要加密的磁盘为盘符为 F。

8. 如果需要收集日志，请转到路径“DRIVE LETTER:\Windows\System32\winevt\Logs”。

9. 将驱动器从恢复计算机分离。

### <a name="create-a-snapshot"></a>创建快照

若要创建快照，请执行[创建磁盘快照](../windows/snapshot-copy-managed-disk.md)中的步骤。

## <a name="collect-an-os-memory-dump"></a>收集 OS 内存转储

当 VM 在配置阶段停滞时，请使用[收集 OS 转储](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)部分中的步骤。

## <a name="contact-microsoft-support"></a>联系 Microsoft 支持部门

收集转储文件后，请联系 [Microsoft 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)来分析根本原因。


## <a name="rebuild-the-vm-by-using-powershell"></a>使用 PowerShell 重新生成 VM

收集内存转储文件后，请按照以下步骤重新生成 VM。

**对于非托管磁盘**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**对于托管磁盘**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
