---
title: 通过 Linux 使用 Azure 文件 | Microsoft Docs
description: 了解如何在 Linux 上通过 SMB 装载 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3d8d7c6d3c4e752480310c122bcb7db237b3022b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209410"
---
# <a name="use-azure-files-with-linux"></a>通过 Linux 使用 Azure 文件
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 可以使用 [SMB 内核客户端](https://wiki.samba.org/index.php/LinuxCIFS)在 Linux 分发版中装载 Azure 文件共享。 本文介绍装载 Azure 文件共享的两种方法：使用 `mount` 命令按需装载，以及通过在 `/etc/fstab` 中创建一个条目在启动时装载。

The recommended way to mount an Azure file share on Linux is using SMB 3.0. By default, Azure Files requires encryption in transit, which is only supported by SMB 3.0. Azure Files also supports SMB 2.1, which does not support encryption in transit, but you may not mount Azure file shares with SMB 2.1 from another Azure region or on-premises for security reasons. Unless your application specifically requires SMB 2.1, there is little reason to use it since most popular, recently released Linux distributions support SMB 3.0:  

| | SMB 2.1 <br>（装载在同一 Azure 区域内的 VM 上） | SMB 3.0 <br>（从本地和跨区域装载） |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

If you're using a Linux distribution not listed in the above table, you can check to see if your Linux distribution supports SMB 3.0 with encryption by checking the Linux kernel version. SMB 3.0 with encryption was added to Linux kernel version 4.11. The `uname` command will return the version of the Linux kernel in use:

```bash
uname -r
```

## <a name="prerequisites"></a>必备组件
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Ensure the cifs-utils package is installed.**  
    可在所选的 Linux 分发版上使用包管理器安装 cifs-utils 包。 

    在 **Ubuntu** 和**基于 Debian** 的分发版上，请使用 `apt` 包管理器：

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    On **Fedora**, **Red Hat Enterprise Linux 8+** , and **CentOS 8 +** , use the `dnf` package manager:

    ```bash
    sudo dnf install cifs-utils
    ```

    On older versions of **Red Hat Enterprise Linux** and **CentOS**, use the `yum` package manager:

    ```bash
    sudo yum install cifs-utils 
    ```

    在 **openSUSE** 上，请使用 `zypper` 包管理器：

    ```bash
    sudo zypper install cifs-utils
    ```

    在其他分发版上，请使用相应的包管理器，或[从源编译](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **The most recent version of the Azure Command Line Interface (CLI).** For more information on how to install the Azure CLI, see [Install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) and select your operating system. If you prefer to use the Azure PowerShell module in PowerShell 6+, you may, however the instructions below are presented for the Azure CLI.

* **确保已打开端口 445**：SMB 通过 TCP 端口 445 通信 - 请查看防火墙是否未阻止 TCP 端口 445 与客户端计算机通信。  Replace **<your-resource-group>** and **<your-storage-account>**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $fileHost | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    If the connection was successful, you should see something similar to the following output:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    If you are unable to open up port 445 on your corporate network or are blocked from doing so by an ISP, you may use a VPN connection or ExpressRoute to work around port 445. For more information, see [Networking considerations for direct Azure file share access](storage-files-networking-overview.md)..

## <a name="mounting-azure-file-share"></a>Mounting Azure file share
To use an Azure file share with your Linux distribution, you must create a directory to serve as the mount point for the Azure file share. A mount point can be created anywhere on your Linux system, but it's common convention to create this under /mnt. After the mount point, you use the `mount` command to access the Azure file share.

You can mount the same Azure file share to multiple mount points if desired.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>使用 `mount` 按需装载 Azure 文件共享
1. **Create a folder for the mount point**: Replace `<your-resource-group>`, `<your-storage-account>`, and `<your-file-share>` with the appropriate information for your environment:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Use the mount command to mount the Azure file share**. In the example below, the local Linux file and folder permissions default 0755, which means read, write, and execute for the owner (based on the file/directory Linux owner), read and execute for users in owner group, and read and execute for others on the system. You can use the `uid` and `gid` mount options to set the user ID and group ID for the mount. You can also use `dir_mode` and `file_mode` to set custom permissions as desired. For more information on how to set permissions, see [UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) on Wikipedia. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > The above mount command mounts with SMB 3.0. If your Linux distribution does not support SMB 3.0 with encryption or if it only supports SMB 2.1, you may only mount from an Azure VM within the same region as the storage account. To mount your Azure file share on a Linux distribution that does not support SMB 3.0 with encryption, you will need to [disable encryption in transit for the storage account](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

使用完 Azure 文件共享后，可以使用 `sudo umount $mntPath` 卸载共享。

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>使用 `/etc/fstab` 为 Azure 文件共享创建持久装入点
1. **Create a folder for the mount point**: A folder for a mount point can be created anywhere on the file system, but it's common convention to create this under /mnt. For example, the following command creates a new directory, replace `<your-resource-group>`, `<your-storage-account>`, and `<your-file-share>` with the appropriate information for your environment:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **创建凭据文件以存储文件共享的用户名（存储帐户名称）和密码（存储帐户密钥）。** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **更改凭据文件的权限，以便只有 root 才能读取或修改密码文件。** 由于存储帐户密钥实质上是存储帐户的超级管理员密码，因此重要的是在文件上设置权限使只有 root 才能访问，这样较低权限的用户将无法检索存储帐户密钥。   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Use the following command to append the following line to `/etc/fstab`** : In the example below, the local Linux file and folder permissions default 0755, which means read, write, and execute for the owner (based on the file/directory Linux owner), read and execute for users in owner group, and read and execute for others on the system. You can use the `uid` and `gid` mount options to set the user ID and group ID for the mount. You can also use `dir_mode` and `file_mode` to set custom permissions as desired. For more information on how to set permissions, see [UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) on Wikipedia.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > The above mount command mounts with SMB 3.0. If your Linux distribution does not support SMB 3.0 with encryption or if it only supports SMB 2.1, you may only mount from an Azure VM within the same region as the storage account. To mount your Azure file share on a Linux distribution that does not support SMB 3.0 with encryption, you will need to [disable encryption in transit for the storage account](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="securing-linux"></a>Securing Linux
In order to mount an Azure file share on Linux, port 445 must be accessible. 许多组织因 SMB 1 中固有的安全风险而阻止端口 445。 SMB 1, also known as CIFS (Common Internet File System), is a legacy file system protocol included with many Linux distributions. SMB 1 是过时且效率不高的协议，而且最重要的是，它是不安全的协议。 The good news is that Azure Files does not support SMB 1, and starting with Linux kernel version 4.18, Linux makes it possible to disable SMB 1. We always [strongly recommend](https://aka.ms/stopusingsmb1) disabling the SMB 1 on your Linux clients before using SMB file shares in production.

Starting with Linux kernel 4.18, the SMB kernel module, called `cifs` for legacy reasons, exposes a new module parameter (often referred to as *parm* by various external documentation), called `disable_legacy_dialects`. Although introduced in Linux kernel 4.18, some vendors have backported this change to older kernels that they support. For convenience, the following table details the availability of this module parameter on common Linux distributions.

| 分配 | Can disable SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | No |
| Ubuntu 18.04 | 是 |
| Ubuntu 19.04+ | 是 |
| Debian 8-9 | No |
| Debian 10+ | 是 |
| Fedora 29+ | 是 |
| CentOS 7 | No | 
| CentOS 8+ | 是 |
| Red Hat Enterprise Linux 6.x-7.x | No |
| Red Hat Enterprise Linux 8+ | 是 |
| openSUSE Leap 15.0 | No |
| openSUSE Leap 15.1+ | 是 |
| openSUSE Tumbleweed | 是 |
| SUSE Linux Enterprise 11.x-12.x | No |
| SUSE Linux Enterprise 15 | No |
| SUSE Linux Enterprise 15.1 | No |

You can check to see if your Linux distribution supports the `disable_legacy_dialects` module parameter via the following command.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

This command should output the following message:

```Output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Before disabling SMB 1, you must check to make sure that the SMB module is not currently loaded on your system (this happens automatically if you have mounted an SMB share). You can do this with the following command, which should output nothing if SMB is not loaded:

```bash
lsmod | grep cifs
```

To unload the module, first unmount all SMB shares (using the `umount` command as described above). You can identify all the mounted SMB shares on your system with the following command:

```bash
mount | grep cifs
```

Once you have unmounted all SMB file shares, it's safe to unload the module. 为此，可以使用 `modprobe` 命令：

```bash
sudo modprobe -r cifs
```

You can manually load the module with SMB 1 unloaded using the `modprobe` command:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Finally, you can check the SMB module has been loaded with the parameter by looking at the loaded parameters in `/sys/module/cifs/parameters`:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

To persistently disable SMB 1 on Ubuntu and Debian-based distributions, you must create a new file (if you don't already have custom options for other modules) called `/etc/modprobe.d/local.conf` with the setting. You can do this with the following command:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

You can verify that this has worked by loading the SMB module:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>反馈
Linux 用户，我们希望倾听意见！

针对 Linux 用户组的 Azure 文件提供了一个论坛，在 Linux 上评估和采用文件存储时，可以在该论坛上共享反馈。 向 [Azure 文件 Linux 用户](mailto:azurefileslinuxusers@microsoft.com)发送电子邮件可加入该用户组。

## <a name="next-steps"></a>后续步骤
请参阅以下链接，获取有关 Azure 文件的更多信息：

* [规划 Azure 文件部署](storage-files-planning.md)
* [常见问题](../storage-files-faq.md)
* [故障排除](storage-troubleshoot-linux-file-connection-problems.md)
