---
title: 适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密 | Microsoft Docs
description: 本文是“适用于 Windows 和 Linux IaaS VM 的 Microsoft Azure 磁盘加密”的附录。
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 98bbcb84-8e6c-4eb2-8490-c2a0c67aad79
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: cf3e9ce055219bccb44c19fd8e77fe39c938c968
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392594"
---
# <a name="appendix-for-azure-disk-encryption"></a>有关 Azure 磁盘加密的附录 
本文是[适用于 IaaS VM 的 Azure 磁盘加密](azure-security-disk-encryption-overview.md)的附录。 请务必先阅读“适用于 IaaS VM 的 Azure 磁盘加密”一文，以理解上下文。 本文介绍如何准备预加密的 VHD 和其他任务。

## <a name="connect-to-your-subscription"></a>连接到订阅
在继续之前，请查看[先决条件](azure-security-disk-encryption-prerequisites.md)一文。 确保满足所有先决条件后，请运行以下 cmdlet 连接到订阅：

### <a name="bkmk_ConnectPSH"></a>使用 PowerShell 连接到订阅

1. 启动 Azure PowerShell 会话，并使用以下命令登录 Azure 帐户：

     ```powershell
     Connect-AzureRmAccount 
     ```
2. 如果有多个订阅，并想要指定其中一个要使用的订阅，请键入以下内容以查看帐户的订阅：
     
     ```powershell
     Get-AzureRmSubscription
     ```
3. 若要指定要使用的订阅，请键入：
 
     ```powershell
      Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. 若要验证配置的订阅是否正确，请键入：
     
     ```powershell
     Get-AzureRmSubscription
     ```
5. 如果需要，请使用 [Connect-AzureAD](/powershell/module/azuread/connect-azuread) 连接到 Azure AD。
     
     ```powershell
     Connect-AzureAD
     ```
6. 若要确认已安装 Azure 磁盘加密 cmdlet，请键入：
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. 根据需要查看 [Azure PowerShell 入门](/powershell/azure/get-started-azureps)和 [AzureAD](/powershell/module/azuread)。

### <a name="bkmk_ConnectCLI"></a>使用 Azure CLI 连接到订阅

1. 使用 [az login](/cli/azure/authenticate-azure-cli#interactive-log-in) 登录到 Azure。 
     
     ```azurecli
     az login
     ```

2. 若要选择登录到的租户，请使用：
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. 如果有多个订阅并想要指定其中的一个，请使用 [az account list](/cli/azure/account#az-account-list) 获取订阅列表，然后使用 [az account set](/cli/azure/account#az-account-set) 指定订阅。
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. 验证安装的版本。
     
     ```azurecli
        az --version
     ``` 

5. 根据需要查看 [Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli)。 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Azure 磁盘加密的示例 PowerShell 脚本 

- **列出订阅中所有已加密的 VM**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzureRmVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **列出 Key Vault 中用于加密 VM 的所有磁盘加密机密** 

     ```azurepowershell-interactive
     Get-AzureKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

## <a name="resource-manager-templates"></a>资源管理器模板

- [创建密钥保管库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) 
 
- [在市场中的新 IaaS Windows VM 上启用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - 此模板使用 Windows Server 2012 库映像创建新的加密 Windows VM。

- [部署使用全磁盘加密的 RHEL 7.2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)
    - 此模板在 Azure 中创建一个完全加密的 RHEL 7.2 VM，该 VM 在 /mnt/raidencrypted 位置装载一个 30 GB 的已加密 OS 驱动器和 200 GB 的 RAID-0 阵列。 有关支持的 Linux 服务器分发版，请参阅[常见问题解答](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)一文。 

- [在适用于 Windows 或 Linux 的预加密 VHD 上启用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)

- [在现有或正在运行的 IaaS Windows VM 上启用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [在现有或正在运行的 IaaS Linux VM 上启用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrt-running-linux-vm)    

- [在正在运行的 Windows IaaS 上禁用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [在正在运行的 Linux VM 上禁用加密](https://aka.ms/decrypt-linuxvm) 
    - 只允许对 Linux VM 的数据卷禁用加密。 

- [从预加密的 VHD/存储 Blob 创建新的加密托管磁盘](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - 在提供预加密 VHD 及其对应加密设置的情况下创建新的加密托管磁盘

- [从库映像创建新的加密 Windows IaaS 托管磁盘 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - 此模板使用 Windows Server 2012 库映像新建包含托管磁盘的加密 Windows VM。
    

## <a name="bkmk_preWin"></a>准备预加密的 Windows VHD
以下部分介绍了必要操作，让你在 Azure IaaS 中准备将预加密的 Windows VHD 部署为加密 VHD。 使用该信息在 Azure Site Recovery 或 Azure 上准备和启动全新的 Windows VM (VHD)。 有关如何准备和上传 VHD 的详细信息，请参阅[上传通用化 VHD 并使用它在 Azure 中创建新的 VM](../virtual-machines/windows/upload-generalized-managed.md)。

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>更新组策略以允许使用非 TPM 保护 OS
在“本地计算机策略” > “计算机设置” > “管理模板” > “Windows 组件”下配置名为“BitLocker 驱动器加密”的 BitLocker 组策略设置。 如下图所示，将此设置更改为“操作系统驱动器” > “启动时需要附加身份验证” > “没有兼容的 TPM 时允许 BitLocker”：

![Azure 中的 Microsoft Antimalware](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>安装 BitLocker 功能组件
对于 Windows Server 2012 或更高版本，请使用以下命令：

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

对于 Windows Server 2008 R2，请使用以下命令：

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>使用 `bdehdcfg` 为 BitLocker 准备 OS 卷
若要压缩 OS 分区并为 BitLocker 准备计算机，请根据需要执行 [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment#using-bitlocker-to-encrypt-volumescommand)：

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>使用 BitLocker 保护 OS 卷
使用 [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) 命令在使用外部密钥保护程序的引导卷上启用加密。 此外将外部密钥（.bek 文件）放在外部驱动器或卷上。 下次重启后，会在系统/引导卷上启用加密。

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> 使用独立的数据/资源 VHD 准备 VM，以使用 BitLocker 获取外部密钥。

## <a name="bkmk_LinuxRunning"></a>在正在运行的 Linux VM 上加密 OS 驱动器

### <a name="prerequisites-for-os-disk-encryption"></a>OS 磁盘加密的先决条件

* VM 必须使用与 [Azure 磁盘加密常见问题解答](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)中列出的 OS 磁盘加密兼容的分发版 
* 必须从 Azure 资源管理器中的市场映像创建 VM。
* Azure VM，至少具有 4 GB RAM（建议大小为 7 GB）。
* （针对 RHEL 和 CentOS）禁用 SELinux。 若要禁用 SELinux，请参阅 [SELinux User's and Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux)（SELinux 用户和管理员指南）中针对 VM 的“4.4.2. Disabling SELinux（4.4.2. 禁用 SELinux）”。
* 禁用 SELinux 后，重启 VM 至少一次。

### <a name="steps"></a>Steps
1. 通过之前指定的分发版之一创建 VM。

 对于 CentOS 7.2，通过专门的映像支持 OS 磁盘加密。 若要使用此映像，请在创建 VM 时将“7.2n”指定为 SKU：

 ```powershell
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. 根据需要配置 VM。 如果打算加密所有（OS + 数据）驱动器，需要指定数据驱动器且可从 /etc/fstab 处装载数据驱动器。

 > [!NOTE]
 > 使用 UUID =... 在 /etc/fstab 中指定数据驱动器（而不是指定 /dev/sdb1 等块设备名称）。 在加密过程中，驱动器的顺序会在 VM 上有所改变。 如果 VM 依赖于特定块设备顺序，加密后将无法装载。

3. 注销 SSH 会话。

4. 若要加密 OS，请在启用加密时将 volumeType 指定为“All”或“OS”。

 > [!NOTE]
 > 未作为 `systemd` 服务运行的所有用户空间进程应使用 `SIGKILL` 终止。 重启 VM。 在正在运行的 VM 上启用 OS 磁盘加密时，请计划 VM 停机时间。

5. 使用[下一部分](#monitoring-os-encryption-progress)中的说明，定期监视加密进度。

6. Get-AzureRmVmDiskEncryptionStatus 显示“VMRestartPending”后，通过登录 VM 或通过 Portal/PowerShell/CLI 重启 VM。
    ```powershell
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
重启之前，建议保存 VM 的[启动诊断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。

## <a name="monitoring-os-encryption-progress"></a>监视 OS 加密进度
可通过三种方法监视 OS 加密进度：

* 使用 `Get-AzureRmVmDiskEncryptionStatus` cmdlet 并检查“ProgressMessage”字段：
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 VM 变为“OS 磁盘加密开始”后，在支持高级存储的 VM 上将需要花费大约 40-50 分钟。

 由于 WALinuxAgent 出现[问题 #388](https://github.com/Azure/WALinuxAgent/issues/388)，`OsVolumeEncrypted` 和 `DataVolumesEncrypted` 在某些发行版中显示为 `Unknown`。 在 WALinuxAgent 2.1.5 版及更高版本中，会自动修复此问题。 如果在输出中看到 `Unknown`，可通过使用 Azure 资源浏览器验证磁盘加密状态。

 转到 [Azure 资源浏览器](https://resources.azure.com/)，并在左侧的选择面板中展开此层次结构：

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 在 InstanceView 中，向下滚动以查看驱动器的加密状态。

 ![VM 实例视图](./media/azure-security-disk-encryption/vm-instanceview.png)

* 查看[启动诊断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。 来自 ADE 扩展的消息应带有前缀 `[AzureDiskEncryption]`。

* 通过 SSH 登录 VM 并从以下位置获取扩展日志：

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 建议不要在 OS 加密正在进行时登录 VM。 仅当其他两个方法都失败时再复制日志。

## <a name="bkmk_preLinux"></a>准备预加密的 Linux VHD
预加密 VHD 的准备过程根据分发版的不同而异。 我们提供了有关准备 [Ubuntu 16](#bkmk_Ubuntu)、[openSUSE 13.2](#bkmk_openSUSE) 和 [CentOS 7](#bkmk_CentOS) 的示例。 

### <a name="bkmk_Ubuntu"></a>Ubuntu 16
通过执行以下步骤在分发安装过程中配置加密：

1. 对磁盘进行分区时选择“配置加密卷”。

 ![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. 创建一个单独的不得加密的启动驱动器。 对根驱动器进行加密。

 ![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. 提供通行短语。 这是上传到 Key Vault 的通行短语。

 ![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. 完成分区。

 ![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. 启动 VM 并被要求提供密码时，请使用步骤 3 中提供的密码。

 ![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. 使用[这些说明](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/)准备 VM 以上传到 Azure。 不要运行最后一步（解除配置 VM）。

执行以下步骤，配置适用于 Azure 的加密：

1. 在 /usr/local/sbin/azure_crypt_key.sh 下创建一个包含以下脚本的文件。 请注意 KeyFileName，因为它是 Azure 使用的密码文件名。

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. 在 */etc/crypttab* 中更改加密配置。 它看起来应该如下所示：
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. 如果要在 Windows 中编辑 *azure_crypt_key.sh*，但将其复制到了 Linux，请运行 `dos2unix /usr/local/sbin/azure_crypt_key.sh`。

4. 将可执行文件的权限添加到脚本：
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. 通过追加行编辑 */etc/initramfs-tools/modules*：
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. 运行 `update-initramfs -u -k all` 更新 initramfs 以使 `keyscript` 生效。

7. 现在可以解除配置 VM。

 ![Ubuntu 16.04 安装](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. 继续执行下一步，将 VHD 上传到 Azure 中。

### <a name="bkmk_openSUSE"></a>openSUSE 13.2
若要在分发版安装期间配置加密，请执行以下步骤：
1. 对磁盘进行分区时，选择“加密卷组”，并输入密码。 这是将上传到 Key Vault 的密码。

 ![openSUSE 13.2 安装](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. 使用密码启动 VM。

 ![openSUSE 13.2 安装](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. 遵循 [Prepare a SLES or openSUSE virtual machine for Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131)（为 Azure 准备 SLES 或 openSUSE 虚拟机）中的说明准备 VM，以上传到 Azure。 不要运行最后一步（解除配置 VM）。

若要配置适用于 Azure 的加密，请执行以下步骤：
1. 编辑 /etc/dracut.conf 并添加以下行：
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. 注释掉文件 /usr/lib/dracut/modules.d/90crypt/module-setup.sh 末尾的这些代码行：
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. 在文件 /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh 的开头附加以下行：
 ```
    DRACUT_SYSTEMD=0
 ```
并更改所有匹配项：
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
修改为：
```
    if [ 1 ]; then
```
4. 编辑 /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh 并将其附加在“# Open LUKS device”的后面：

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. 运行 `/usr/sbin/dracut -f -v` 以更新 initrd。

6. 现在可以解除配置 VM，并将 VHD 上传到 Azure 中。

### <a name="bkmk_CentOS"></a>CentOS 7
若要在分发版安装期间配置加密，请执行以下步骤：
1. 对磁盘进行分区时，选择“加密我的数据”。

 ![CentOS 7 安装](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. 确保为根分区选择了“加密”。

 ![CentOS 7 安装](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. 提供通行短语。 这是要上传到 Key Vault 的通行短语。

 ![CentOS 7 安装](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. 启动 VM 并被要求提供密码时，请使用步骤 3 中提供的密码。

 ![CentOS 7 安装](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. 通过 [Prepare a CentOS-based virtual machine for Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70)（为 Azure 准备基于 CentOS 的虚拟机）中的“CentOS 7.0+”说明准备 VM 以上传到 Azure。 不要运行最后一步（解除配置 VM）。

6. 现在可以解除配置 VM，并将 VHD 上传到 Azure 中。

若要配置适用于 Azure 的加密，请执行以下步骤：

1. 编辑 /etc/dracut.conf 并添加以下行：
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. 注释掉文件 /usr/lib/dracut/modules.d/90crypt/module-setup.sh 末尾的这些代码行：
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. 在文件 /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh 的开头附加以下行：
```
    DRACUT_SYSTEMD=0
```
并更改所有匹配项：
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
to
```
    if [ 1 ]; then
```
4. 编辑 /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh 并将其附加在“# Open LUKS device”的后面：
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. 运行“/usr/sbin/dracut -f -v”以更新 initrd。

![CentOS 7 安装](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a>将加密的 VHD 上传到 Azure 存储帐户
启用 BitLocker 加密或 DM-Crypt 加密后，需要将本地加密的 VHD 上传到存储帐户。
```powershell
    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a>将预加密 VM 的机密上传到 Key Vault
需要将前面获取的磁盘加密机密作为机密上传到 Key Vault 中。 Key Vault 需要具有对 Azure AD 客户端启用的磁盘加密等权限。

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a>未使用 KEK 加密的磁盘加密机密
使用 [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) 在 Key Vault 中设置机密。 对于 Windows 虚拟机，需将 bek 文件编码为 base64 字符串，并使用 `Set-AzureKeyVaultSecret` cmdlet 将其上传到 Key Vault。 对于 Linux，需将密码编码为 base64 字符串，并将其上传到 Key Vault。 此外，请确保在 Key Vault 中创建机密时设置以下标记。

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


在下一步中使用 `$secretUrl` 以便[在不使用 KEK 的情况下附加 OS 磁盘](#bkmk_URLnoKEK)。

### <a name="bkmk_SecretKEK"></a>使用 KEK 加密的磁盘加密机密
将机密上传到 Key Vault 之前，可根据需要使用密钥加密密钥对其进行加密。 先使用包装 [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) 加密使用密钥加密密钥的机密。 此包装操作的输出是 base64 URL 编码的字符串，可以使用 [`Set-AzureKeyVaultSecret`](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) cmdlet 将其作为机密上传。

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

将在下一步中使用 `$KeyEncryptionKey` 和 `$secretUrl` 以便[在使用 KEK 的情况下附加 OS 磁盘](#BKMK_URLKEK)。

##  <a name="bkmk_SecretURL"></a>附加 OS 磁盘时指定机密 URL

###  <a name="bkmk_URLnoKEK"></a>不使用 KEK
附加 OS 磁盘时，需要传递 `$secretUrl`。 该 URL 是在“不使用 KEK 对磁盘加密机密进行加密”部分中生成的。
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a>使用 KEK
附加 OS 磁盘时，传递 `$KeyEncryptionKey` 和 `$secretUrl`。 该 URL 是在“使用 KEK 加密的磁盘加密机密”部分生成的。
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
