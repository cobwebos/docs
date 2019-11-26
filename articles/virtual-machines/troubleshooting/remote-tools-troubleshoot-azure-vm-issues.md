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
ms.openlocfilehash: 3f028431fcd4b338d2e610ce1828a02b753c4d32
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483698"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>使用远程工具排查 Azure VM 问题

When you troubleshoot issues on an Azure virtual machine (VM), you can connect to the VM by using the remote tools that are discussed in this article instead of using the Remote Desktop Protocol (RDP).

## <a name="serial-console"></a>串行控制台

Use a [serial console for Azure Virtual Machines](serial-console-windows.md) to run commands on the remote Azure VM.

## <a name="remote-cmd"></a>远程 CMD

下载 [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)。 运行以下命令连接到 VM：

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* The command must be run on a computer that's in the same virtual network.
>* DIP or HostName can be used to replace \<computer>.
>* -s 参数确保使用系统帐户（管理员权限）调用命令。
>* PsExec 使用 TCP 端口 135 和 445。 As a result, the two ports have to be open on the firewall.

## <a name="run-command"></a>运行命令

For more information about how to use the run command feature to run scripts on the VM, see [Run PowerShell scripts in your Windows VM with run command](../windows/run-command.md).

## <a name="custom-script-extension"></a>自定义脚本扩展

可以使用“自定义脚本扩展”功能在目标 VM 上运行自定义脚本。 若要使用此功能，必须符合以下条件：

* VM 已建立连接。
* Azure Virtual Machine Agent is installed and is working as expected on the VM.
* The extension wasn't previously installed on the VM.
 
  The extension injects the script only the first time that it's used. If you use this feature later, the extension recognizes that it was already used and doesn't upload the new script.

Upload your script to a storage account, and generate its own container. 然后，在已连接到 VM 的计算机上的 Azure PowerShell 中运行以下脚本。

### <a name="for-classic-deployment-model-vms"></a>For classic deployment model VMs

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

### <a name="for-azure-resource-manager-vms"></a>For Azure Resource Manager VMs

 

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
>For Azure Resource Manager VMs, you must open port 5986 on the network security group (NSG). 有关详细信息，请参阅“安全组”。 
>
>对于 RDFE VM，必须有一个配备专用端口 (5986) 和公共端口的终结点。 Then, you also have to open that public-facing port on the NSG.

### <a name="set-up-the-client-computer"></a>设置客户端计算机

若要使用 PowerShell 远程连接到 VM，首先需要设置客户端计算机，以允许建立连接。 为此，请相应地运行以下命令，将 VM 添加到 PowerShell 信任的主机列表。

To add one VM to the trusted hosts list:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

To add multiple VMs to the trusted hosts list:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

将所有计算机添加到信任的主机列表：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>在 VM 上启用 RemotePS

For VMs created using the classic deployment model, use the Custom Script Extension to run the following script:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

For Azure Resource Manager VMs, use run commands from the portal to run the EnableRemotePS script:

![运行命令](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>连接到 VM

Run the following command based on the client computer location:

* Outside the virtual network or deployment

  * For a VM created using the classic deployment model, run the following command:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * For an Azure Resource Manager VM, first add a DNS name to the public IP address. 有关详细步骤，请参阅[在 Azure 门户中创建 Windows VM 的完全限定域名](../windows/portal-create-fqdn.md)。 然后，运行以下命令：

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Inside the virtual network or deployment, run the following command:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>如果设置 SkipCaCheck 标志，则启动会话时无需将证书导入 VM。

You can also use the Invoke-Command cmdlet to run a script on the VM remotely.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>远程注册表

>[!NOTE]
>必须打开 TCP 端口 135 或 445 才能使用此选项。
>
>For Azure Resource Manager VMs, you have to open port 5986 on the NSG. 有关详细信息，请参阅“安全组”。 
>
>对于 RDFE VM，必须有一个配备专用端口 5986 和公共端口的终结点。 You also have to open that public-facing port on the NSG.

1. From another VM on the same virtual network, open the registry editor (regedit.exe).

2. 选择“文件” > “连接网络注册表”。

   ![Registry editor](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Locate the target VM by **host name** or **dynamic IP** (preferable) by entering it in the **Enter the object name to select** box.

   ![Enter the object name to select box](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. 输入目标 VM 的凭据。

5. 进行任何必要的注册表更改。

## <a name="remote-services-console"></a>Remote services console

>[!NOTE]
>必须打开 TCP 端口 135 或 445 才能使用此选项。
>
>For Azure Resource Manager VMs, you have to open port 5986 on the NSG. 有关详细信息，请参阅“安全组”。 
>
>对于 RDFE VM，必须有一个配备专用端口 5986 和公共端口的终结点。 You also have to open that public-facing port on the NSG.

1. From another VM on the same virtual network, open an instance of **Services.msc**.

2. 右键单击“服务(本地)”。

3. 选择“连接到另一台计算机”。

   ![远程服务](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Enter the dynamic IP of the target VM.

   ![Input dynamic IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. 对服务进行任何必要的更改。

## <a name="next-steps"></a>后续步骤

- For more information about the Enter-PSSession cmdlet, see [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- For more information about the Custom Script Extension for Windows using the classic deployment model, see [Custom Script Extension for Windows](../extensions/custom-script-classic.md).
- PsExec 包含在 [PSTools Suite](https://download.sysinternals.com/files/PSTools.zip) 中。
- For more information about the PSTools Suite, see [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


