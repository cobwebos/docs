---
title: 使用远程工具排查 Azure VM 问题 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: 513ce98703e67053ab0bcac3e6fc7a3e959f6870
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307355"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>使用远程工具排查 Azure VM 问题

排查 Azure 虚拟机 (VM) 的问题时，可以使用本文所述的远程工具而不是远程桌面协议 (RDP) 连接到 VM。

## <a name="serial-console"></a>串行控制台

使用[虚拟机串行控制台](serial-console-windows.md)可在远程 Azure VM 上运行命令。

## <a name="remote-cmd"></a>远程 CMD

下载 [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)。 运行以下命令连接到 VM：

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* 必须在位于同一 VNET 中的计算机上运行该命令。
>* 可以使用 DIP 或主机名替换\<计算机 >。
>* -s 参数确保使用系统帐户（管理员权限）调用命令。
>* PsExec 使用 TCP 端口 135 和 445。 因此，需要在防火墙中打开这两个端口。

## <a name="run-commands"></a>运行命令

有关如何使用“运行命令”功能在 VM 上运行脚本的详细信息，请参阅[使用“运行命令”在 Windows VM 中运行 PowerShell 脚本](../windows/run-command.md)。

## <a name="customer-script-extension"></a>自定义脚本扩展

可以使用“自定义脚本扩展”功能在目标 VM 上运行自定义脚本。 若要使用此功能，必须符合以下条件：

* VM 已建立连接。

* 已在 VM 上安装 Azure 代理，并且该代理正在按预期方式运行。

* 未事先在 VM 上安装该扩展。
 
  该扩展只会在首次使用时才注入脚本。 如果以后再使用此功能，该扩展将识别到它已被用过，因此不会上传新脚本。

必须将脚本上传到存储帐户，并生成该帐户自身的容器。 然后，在已连接到 VM 的计算机上的 Azure PowerShell 中运行以下脚本。

### <a name="for-v1-vms"></a>对于 V1 VM

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>对于 V2 VM

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>远程 PowerShell

>[!Note]
>必须打开 TCP 端口 5986 (HTTPS)，以便能够使用此选项。
>
>对于 ARM VM，必须在网络安全组 (NSG) 中打开端口 5986。 有关详细信息，请参阅“安全组”。 
>
>对于 RDFE VM，必须有一个配备专用端口 (5986) 和公共端口的终结点。 然后，还必须在 NSG 中打开该公共端口。

### <a name="set-up-the-client-computer"></a>设置客户端计算机

若要使用 PowerShell 远程连接到 VM，首先需要设置客户端计算机，以允许建立连接。 为此，请相应地运行以下命令，将 VM 添加到 PowerShell 信任的主机列表。

将一个 VM 添加到信任的主机列表：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

将多个 VM 添加到信任的主机列表：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

将所有计算机添加到信任的主机列表：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>在 VM 上启用 RemotePS

对于经典 VM，请使用自定义脚本扩展运行以下脚本：

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

对于 ARM VM，请在门户中使用“运行命令”来运行 EnableRemotePS 脚本：

![运行命令](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>连接到 VM

根据客户端计算机的位置运行以下命令：

* 在 VNET 或部署的外部

  * 对于经典 VM，请运行以下命令：

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * 对于 ARM VM，请先将一个 DNS 名称添加到公共 IP 地址。 有关详细步骤，请参阅[在 Azure 门户中创建 Windows VM 的完全限定域名](../windows/portal-create-fqdn.md)。 然后，运行以下命令：

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* 在 VNET 或部署中运行以下命令：
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>如果设置 SkipCaCheck 标志，则启动会话时无需将证书导入 VM。

还可以使用 Invoke-Command cmdlet 在 VM 上远程运行脚本：

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>远程注册表

>[!Note]
>必须打开 TCP 端口 135 或 445 才能使用此选项。
>
>对于 ARM VM，必须在 NSG 中打开端口 5986。 有关详细信息，请参阅“安全组”。 
>
>对于 RDFE VM，必须有一个配备专用端口 5986 和公共端口的终结点。 还必须在 NSG 中打开该公共端口。

1. 在同一 VNET 中的另一个 VM 上，打开注册表编辑器 (regedit.exe)。

2. 选择“文件” >“连接网络注册表”。

   ![远程选项](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. 在“输入要选择的对象名称”框中输入目标 VM 的**主机名**或**动态 IP**（首选），以找到该 VM。

   ![远程选项](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. 输入目标 VM 的凭据。

5. 进行任何必要的注册表更改。

## <a name="remote-services-console"></a>远程服务控制台

>[!Note]
>必须打开 TCP 端口 135 或 445 才能使用此选项。
>
>对于 ARM VM，必须在 NSG 中打开端口 5986。 有关详细信息，请参阅“安全组”。 
>
>对于 RDFE VM，必须有一个配备专用端口 5986 和公共端口的终结点。 然后，还必须在 NSG 中打开该公共端口。

1. 在同一 VNET 中的另一个 VM 上，打开 **Services.msc** 的实例。

2. 右键单击“服务(本地)”。

3. 选择“连接到另一台计算机”。

   ![远程服务](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. 输入目标 VM 的动态 IP。

   ![输入 DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. 对服务进行任何必要的更改。

## <a name="next-steps"></a>后续步骤

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[适用于 Windows 的自定义脚本扩展（使用经典部署模型）](../extensions/custom-script-classic.md)

PsExec 包含在 [PSTools Suite](https://download.sysinternals.com/files/PSTools.zip) 中。

有关 PSTools Suite 的详细信息，请参阅 [PSTools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools)。


