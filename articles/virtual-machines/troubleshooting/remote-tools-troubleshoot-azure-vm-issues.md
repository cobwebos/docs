---
title: 使用远程工具排查 Azure VM 问题 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: b86b1a2d8a49554cc3df99e0a32a2c0ccaacb560
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920002"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>使用远程工具排查 Azure VM 问题

当你对 Azure 虚拟机（VM）上的问题进行故障排除时，你可以使用本文中讨论的远程工具（而不是使用远程桌面协议（RDP））连接到 VM。

## <a name="serial-console"></a>串行控制台

使用[Azure 虚拟机的串行控制台](serial-console-windows.md)在远程 Azure VM 上运行命令。

## <a name="remote-cmd"></a>远程 CMD

下载 [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)。 运行以下命令连接到 VM：

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* 此命令必须在同一虚拟网络中的计算机上运行。
>* 可以使用 DIP 或 HostName 来替换 \<计算机 >。
>* -s 参数确保使用系统帐户（管理员权限）调用命令。
>* PsExec 使用 TCP 端口 135 和 445。 因此，必须在防火墙上打开两个端口。

## <a name="run-command"></a>运行命令

有关如何使用运行命令功能在 VM 上运行脚本的详细信息，请参阅[在 WINDOWS VM 中使用 run 命令运行 PowerShell 脚本](../windows/run-command.md)。

## <a name="custom-script-extension"></a>自定义脚本扩展

可以使用“自定义脚本扩展”功能在目标 VM 上运行自定义脚本。 若要使用此功能，必须符合以下条件：

* VM 已建立连接。
* Azure 虚拟机代理已安装，并在 VM 上按预期方式工作。
* 此扩展先前未安装在 VM 上。
 
  此扩展仅在第一次使用脚本时插入该脚本。 如果以后使用此功能，扩展会识别出它已被使用，并且不会上载新脚本。

将脚本上传到存储帐户，并生成自己的容器。 然后，在已连接到 VM 的计算机上的 Azure PowerShell 中运行以下脚本。

### <a name="for-classic-deployment-model-vms"></a>对于经典部署模型 Vm

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>对于 Azure 资源管理器 Vm

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>远程 PowerShell

>[!NOTE]
>必须打开 TCP 端口 5986 (HTTPS)，以便能够使用此选项。
>
>对于 Azure 资源管理器 Vm，必须在网络安全组（NSG）上打开端口5986。 有关详细信息，请参阅“安全组”。 
>
>对于 RDFE VM，必须有一个配备专用端口 (5986) 和公共端口的终结点。 然后，还必须在 NSG 上打开该面向公众的端口。

### <a name="set-up-the-client-computer"></a>设置客户端计算机

若要使用 PowerShell 远程连接到 VM，首先需要设置客户端计算机，以允许建立连接。 为此，请相应地运行以下命令，将 VM 添加到 PowerShell 信任的主机列表。

将一个 VM 添加到受信任的主机列表：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

将多个 Vm 添加到受信任的主机列表：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

将所有计算机添加到信任的主机列表：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>在 VM 上启用 RemotePS

对于使用经典部署模型创建的 Vm，请使用自定义脚本扩展运行以下脚本：

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

对于 Azure 资源管理器 Vm，请使用门户中的 "运行命令" 运行 EnableRemotePS 脚本：

![运行命令](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>连接到 VM

根据客户端计算机位置运行以下命令：

* 在虚拟网络或部署外部

  * 对于使用经典部署模型创建的 VM，请运行以下命令：

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * 对于 Azure 资源管理器 VM，请首先将 DNS 名称添加到公共 IP 地址。 有关详细步骤，请参阅[在 Azure 门户中创建 Windows VM 的完全限定域名](../windows/portal-create-fqdn.md)。 然后，运行以下命令：

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* 在虚拟网络或部署中运行以下命令：
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>如果设置 SkipCaCheck 标志，则启动会话时无需将证书导入 VM。

你还可以使用调用命令 cmdlet 远程在 VM 上运行脚本。

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>远程注册表

>[!NOTE]
>必须打开 TCP 端口 135 或 445 才能使用此选项。
>
>对于 Azure 资源管理器 Vm，必须在 NSG 上打开端口5986。 有关详细信息，请参阅“安全组”。 
>
>对于 RDFE VM，必须有一个配备专用端口 5986 和公共端口的终结点。 还必须在 NSG 上打开该面向公众的端口。

1. 从同一虚拟网络上的另一个 VM，打开注册表编辑器（regedit.exe）。

2. 选择“文件” **“连接网络注册表”。**  > 

   ![注册表编辑器](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. 在 "**输入要选择的对象名称**" 框中输入，按**主机名**或**动态 IP**定位目标 VM （首选）。

   ![输入要选择的对象名称框](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. 输入目标 VM 的凭据。

5. 进行任何必要的注册表更改。

## <a name="remote-services-console"></a>远程服务控制台

>[!NOTE]
>必须打开 TCP 端口 135 或 445 才能使用此选项。
>
>对于 Azure 资源管理器 Vm，必须在 NSG 上打开端口5986。 有关详细信息，请参阅“安全组”。 
>
>对于 RDFE VM，必须有一个配备专用端口 5986 和公共端口的终结点。 还必须在 NSG 上打开该面向公众的端口。

1. 从同一虚拟网络上的另一个 VM，打开**services.msc**的实例。

2. 右键单击“服务(本地)”。

3. 选择“连接到另一台计算机”。

   ![远程服务](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. 输入目标 VM 的动态 IP。

   ![输入动态 IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. 对服务进行任何必要的更改。

## <a name="next-steps"></a>后续步骤

- 有关 node.js cmdlet 的详细信息，请参阅[enter-pssession](https://technet.microsoft.com/library/hh849707.aspx)。
- 有关使用经典部署模型的适用于 Windows 的自定义脚本扩展的详细信息，请参阅[适用于 windows 的自定义脚本扩展](../extensions/custom-script-classic.md)。
- PsExec 包含在 [PSTools Suite](https://download.sysinternals.com/files/PSTools.zip) 中。
- 有关 PSTools 套件的详细信息，请参阅[PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)。


