---
title: 排查 Azure VM 上的 BitLocker 启动错误 | Microsoft Docs
description: 了解如何排查 Azure VM 中的 BitLocker 启动错误
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2019
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: 67a3ba99e29582c5681d69cd0c6db377a258020a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201350"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Azure VM 上的 BitLocker 启动错误

 本文介绍在 Microsoft Azure 中启动 Windows 虚拟机 (VM) 时可能遇到的 BitLocker 错误。

 

## <a name="symptom"></a>症状

 Windows VM 不启动。 检查[启动诊断](../windows/boot-diagnostics.md)窗口中的屏幕截图时，看到以下错误消息之一：

- 插入含 BitLocker 密钥的 USB 驱动程序

- 你被锁定！ 输入恢复密钥，以便再次开始操作（键盘布局：美式键盘）错误登录信息输入次数过多，因此，你的 PC 被锁定以保护你的隐私。 若要检索恢复密钥，请从另一电脑或移动设备转到 https://windows.microsoft.com/recoverykeyfaq 。 如果需要，密钥 ID 为 XXXXXXX。 或者，可以重置电脑。

- 输入密码以解锁此驱动器 [ ] 按 Insert 键在键入时查看密码。
- 输入恢复密钥 从 USB 设备加载恢复密钥。

## <a name="cause"></a>原因

如果 VM 找不到用于解密加密磁盘的 BitLocker 恢复密钥 (BEK) 文件，则可能会出现此问题。

## <a name="solution"></a>解决方案

若要解决此问题，停止并解除分配 VM，然后重启。 此操作将强制 VM 从 Azure Key Vault 中检索 BEK 文件，然后将其放在加密磁盘上。 

如果此方法未能解决此问题，请执行以下步骤，手动还原 BEK 文件：

1. 拍摄受影响的 VM 的系统磁盘的快照作为备份。 有关详细信息，请参阅[拍摄磁盘快照](../windows/snapshot-copy-managed-disk.md)。
2. [将系统磁盘附加到恢复 VM](troubleshoot-recovery-disks-portal-windows.md)。 若要在步骤 7 中运行 [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) 命令，必须在恢复 VM 中启用“BitLocker 驱动器加密”  功能。

    附加托管磁盘时，可能会收到“包含加密设置，因此不能用作数据磁盘”错误消息。 在此情况下，运行以下脚本，重试附加磁盘：

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     不能将托管磁盘附加到从 Blob 映像还原的 VM。

3. 附加磁盘后，对恢复 VM 进行远程桌面连接，以便可以运行某些 Azure PowerShell 脚本。 确保已在恢复 VM 上安装[最新版本的 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。

4. 打开提升的 Azure PowerShell 会话（以管理员身份运行）。 运行以下命令来登录到 Azure 订阅：

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. 运行以下脚本来检查 BEK 文件的名称：

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    下面是输出的示例。 找到附加磁盘的 BEK 文件名。 在此情况下，假定附加磁盘的驱动器号为 F，且 BEK 文件为 EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK。

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    如果看到两个重复的卷，具有较新时间戳的卷为恢复 VM 使用的当前 BEK 文件。

    如果“内容类型”  值为“包装的 BEK”  ，请转到[密钥加密密钥 (KEK) 方案](#key-encryption-key-scenario)。

    获取驱动器的 BEK 文件名称后，须创建 secret-file-name.BEK 文件以解锁驱动器。

6.  将 BEK 文件下载到恢复磁盘。 以下示例将 BEK 文件保存到 C:\BEK 文件夹。 运行脚本前，请确保 `C:\BEK\` 路径存在。

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  若要使用 BEK 文件解锁附加磁盘，请运行以下命令。

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    在此示例中，附加的 OS 磁盘为驱动器 F。请确保使用正确的驱动器号。 

8. 使用 BEK 密钥成功解锁磁盘以后，从恢复 VM 分离该磁盘，然后使用该新的 OS 磁盘重新创建 VM。

    > [!NOTE]
    > 对于使用磁盘加密的 VM，不支持交换 OS 磁盘。

9. 如果新的 VM 仍然不能正常启动，请在解锁设备后尝试下述步骤之一：

    - 暂停保护，以便运行以下命令，暂时关闭 BitLocker：

                    manage-bde -protectors -disable F: -rc 0
           
    - 完全解密该驱动器。 为此，请运行以下命令：

                    manage-bde -off F:

### <a name="key-encryption-key-scenario"></a>密钥加密密钥方案

对于密钥加密密钥方案，请执行以下步骤：

1. 请确保登录的用户帐户需要“用户|密钥权限|加密操作|解包密钥”  中 Key Vault 访问策略中的“解包”权限。
2. 将以下脚本保存到 .PS1 文件：

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. 设置参数。 该脚本处理 KEK 机密以创建 BEK 密钥，然后将其保存到恢复 VM 上的本地文件夹中。 如果在运行脚本时收到错误，请参阅[脚本故障排除](#script-troubleshooting)部分。

4. 脚本开始时，将看到以下输出：

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    脚本完成后，将看到以下输出：

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. 若要使用 BEK 文件解锁附加磁盘，请运行以下命令：

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    在此示例中，附加的 OS 磁盘为驱动器 F。请确保使用正确的驱动器号。 

6. 使用 BEK 密钥成功解锁磁盘以后，从恢复 VM 分离该磁盘，然后使用该新的 OS 磁盘重新创建 VM。 

    > [!NOTE]
    > 对于使用磁盘加密的 VM，不支持交换 OS 磁盘。

7. 如果新的 VM 仍然不能正常启动，请在解锁设备后尝试下述步骤之一：

    - 暂停保护，以便运行以下命令，暂时关闭 BitLocker：

             manage-bde -protectors -disable F: -rc 0
           
    - 完全解密该驱动器。 为此，请运行以下命令：

                    manage-bde -off F:
## <a name="script-troubleshooting"></a>脚本故障排除

**错误：无法加载文件或程序集**

发出此错误是因为 ADAL 程序集的路径错误。 如果 AZ 模块只为当前用户安装，则 ADAL 程序集将位于 `C:\Users\<username>\Documents\WindowsPowerShell\Modules\Az.Accounts\<version>` 中。

也可搜索 `Az.Accounts` 文件夹来查找正确的路径。

**错误：Get-AzKeyVaultSecret 或 Get-AzKeyVaultSecret 无法识别为 cmdlet 的名称**

如果使用旧的 AZ PowerShell 模块，则必须将这两个命令更改为 `Get-AzureKeyVaultSecret` 和 `Get-AzureKeyVaultSecret`。

**参数示例**

| parameters  | 值示例  |注释   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | 用于存储此密钥的密钥保管库的名称 |
|$kekName   |mykey   | 用于加密 VM 的密钥的名称|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | VM 密钥的机密的名称|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D.BEK |用于写入 BEK 文件的路径。|
|$adTenant  |contoso.onmicrosoft.com   | 用于托管密钥保管库的 Azure Active Directory 的 FQDN 或 GUID |
