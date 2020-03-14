---
title: 通过 Linux 使用 Azure 文件 | Microsoft Docs
description: 了解如何在 Linux 上通过 SMB 装载 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b2469a2b5819b3011f919a2b483933bb030eed70
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268192"
---
# <a name="use-azure-files-with-linux"></a>通过 Linux 使用 Azure 文件
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 可以使用 [SMB 内核客户端](https://wiki.samba.org/index.php/LinuxCIFS)在 Linux 分发版中装载 Azure 文件共享。 本文介绍装载 Azure 文件共享的两种方法：使用 `mount` 命令按需装载，以及通过在 `/etc/fstab` 中创建一个条目在启动时装载。

在 Linux 上装载 Azure 文件共享的建议方法是使用 SMB 3.0。 默认情况下，Azure 文件要求在传输过程中加密，这仅受 SMB 3.0 的支持。 Azure 文件还支持 SMB 2.1，该功能不支持传输中的加密，但出于安全原因，你可能不会从其他 Azure 区域或本地装载 SMB 2.1 的 Azure 文件共享。 除非你的应用程序特别需要 SMB 2.1，否则很少需要使用它，因为最近发布的最常见 Linux 发行版支持 SMB 3.0：  

| | SMB 2.1 <br>（装载在同一 Azure 区域内的 VM 上） | SMB 3.0 <br>（从本地和跨区域装载） |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10+ |
| OpenSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

如果你使用的是上表中未列出的 Linux 分发版，则可以通过检查 Linux 内核版本来查看 Linux 发行版是否支持使用加密的 SMB 3.0。 已将包含加密的 SMB 3.0 添加到 Linux 内核版本4.11。 `uname` 命令将返回正在使用的 Linux 内核版本：

```bash
uname -r
```

## <a name="prerequisites"></a>必备条件
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**确保安装了 utils 包。**  
    可在所选的 Linux 分发版上使用包管理器安装 cifs-utils 包。 

    在 **Ubuntu** 和**基于 Debian** 的分发版上，请使用 `apt` 包管理器：

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    在**Fedora**上， **Red Hat Enterprise Linux 8 +** 和**CentOS 8 +** ，请使用 `dnf` 程序包管理器：

    ```bash
    sudo dnf install cifs-utils
    ```

    在较旧版本的**Red Hat Enterprise Linux**和**CentOS**上，使用 `yum` 程序包管理器：

    ```bash
    sudo yum install cifs-utils 
    ```

    在 **openSUSE** 上，请使用 `zypper` 包管理器：

    ```bash
    sudo zypper install cifs-utils
    ```

    在其他分发版上，请使用相应的包管理器，或[从源编译](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **最新版本的 Azure 命令行接口（CLI）。** 有关如何安装 Azure CLI 的详细信息，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)并选择操作系统。 如果更愿意使用 PowerShell 6 + 中的 Azure PowerShell 模块，则可以使用以下说明来获取 Azure CLI。

* **确保已打开端口 445**：SMB 通过 TCP 端口 445 通信 - 请查看防火墙是否未阻止 TCP 端口 445 与客户端计算机通信。  替换 **< 资源组 >** 并 **< 存储帐户 >**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    如果连接成功，则会看到类似于以下输出的内容：

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    如果无法在公司网络上打开端口445，或 ISP 阻止其执行此操作，则可以使用 VPN 连接或 ExpressRoute 来解决端口445。 有关详细信息，请参阅[直接 Azure 文件共享访问的网络注意事项](storage-files-networking-overview.md)。

## <a name="mounting-azure-file-share"></a>装载 Azure 文件共享
若要将 Azure 文件共享与 Linux 分发一起使用，必须创建一个目录用作 Azure 文件共享的装入点。 可以在 Linux 系统上的任何位置创建装入点，但在/mnt。下创建它通常是一种常见约定。 在装入点之后，使用 `mount` 命令访问 Azure 文件共享。

如果需要，可以将同一个 Azure 文件共享装载到多个装入点。

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>使用 `mount` 按需装载 Azure 文件共享
1. 为**装入点创建一个文件夹**：将 `<your-resource-group>`、`<your-storage-account>`和 `<your-file-share>` 替换为适用于你的环境的相应信息：

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **使用 mount 命令装载 Azure 文件共享**。 在下面的示例中，本地 Linux 文件和文件夹权限默认为0755，这意味着所有者的读取、写入和执行（基于文件/目录 Linux 所有者），对所有者组中的用户进行读取和执行，并读取和执行系统中的其他用户。 你可以使用 "`uid`" 和 "`gid` 装载选项" 来设置装载的用户 ID 和组 ID。 还可以根据需要使用 `dir_mode` 和 `file_mode` 设置自定义权限。 有关如何设置权限的详细信息，请参阅维基百科上的[UNIX 数值表示法](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)。 

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
    > 上述 mount 命令装载 SMB 3.0。 如果 Linux 发行版不支持使用加密的 SMB 3.0，或者它仅支持 SMB 2.1，则只能从存储帐户所在的同一区域内的 Azure VM 进行装载。 若要在不支持使用加密的 SMB 3.0 的 Linux 分发版上装载 Azure 文件共享，需要为[存储帐户禁用传输加密](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

使用完 Azure 文件共享后，可以使用 `sudo umount $mntPath` 卸载共享。

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>使用 `/etc/fstab` 为 Azure 文件共享创建持久装入点
1. **为装入点创建一个文件夹**：可在文件系统上的任何位置创建装入点的文件夹，但在/mnt。下创建该文件夹是常见约定。 例如，以下命令将创建一个新目录，将 `<your-resource-group>`、`<your-storage-account>`和 `<your-file-share>` 替换为适用于您的环境的相应信息：

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

1. **使用以下命令将以下行追加到 `/etc/fstab`** ：在下面的示例中，本地 Linux 文件和文件夹权限默认为0755，这意味着所有者的读取、写入和执行（基于文件/目录的 Linux 所有者），对所有者组中的用户进行读取和执行，并读取和执行系统中的其他用户。 你可以使用 "`uid`" 和 "`gid` 装载选项" 来设置装载的用户 ID 和组 ID。 还可以根据需要使用 `dir_mode` 和 `file_mode` 设置自定义权限。 有关如何设置权限的详细信息，请参阅维基百科上的[UNIX 数值表示法](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)。

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
    > 上述 mount 命令装载 SMB 3.0。 如果 Linux 发行版不支持使用加密的 SMB 3.0，或者它仅支持 SMB 2.1，则只能从存储帐户所在的同一区域内的 Azure VM 进行装载。 若要在不支持使用加密的 SMB 3.0 的 Linux 分发版上装载 Azure 文件共享，需要为[存储帐户禁用传输加密](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="securing-linux"></a>保护 Linux
若要在 Linux 上装载 Azure 文件共享，必须可以访问端口445。 许多组织因 SMB 1 中固有的安全风险而阻止端口 445。 SMB 1 （也称为 CIFS （通用 Internet 文件系统））是许多 Linux 发行版中包含的传统文件系统协议。 SMB 1 是过时且效率不高的协议，而且最重要的是，它是不安全的协议。 好消息是，Azure 文件不支持 SMB 1，从 Linux 内核版本4.18 开始，Linux 使你可以禁用 SMB 1。 我们始终[强烈建议](https://aka.ms/stopusingsmb1)在生产环境中使用 smb 文件共享之前，在 Linux 客户端上禁用 smb 1。

从 Linux 内核4.18 开始，作为旧原因被称为 `cifs` 的 SMB 内核模块公开了新的模块参数（通常称为*parm* ），称为 `disable_legacy_dialects`。 尽管在 Linux 内核4.18 中引入，但某些供应商已将此更改向后移植到它们支持的旧内核。 为方便起见，下表详细介绍了常见 Linux 发行版上此模块参数的可用性。

| 分发 | 可以禁用 SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | 否 |
| Ubuntu 18.04 | 是 |
| Ubuntu 19.04 + | 是 |
| Debian 8-9 | 否 |
| Debian 10 + | 是 |
| Fedora 29 + | 是 |
| CentOS 7 | 否 | 
| CentOS 8 + | 是 |
| Red Hat Enterprise Linux 1.x-7. x | 否 |
| Red Hat Enterprise Linux 8 + | 是 |
| openSUSE Leap 15。0 | 否 |
| openSUSE Leap 15.1 + | 是 |
| openSUSE Tumbleweed | 是 |
| SUSE Linux Enterprise 11. x-12. x | 否 |
| SUSE Linux Enterprise 15 | 否 |
| SUSE Linux Enterprise 15。1 | 否 |

可以通过以下命令查看 Linux 发行版是否支持 `disable_legacy_dialects` module 参数。

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

此命令应输出以下消息：

```Output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

禁用 SMB 1 之前，必须检查以确保系统中当前未加载 SMB 模块（如果已安装 SMB 共享，则会自动发生此情况）。 你可以通过以下命令来执行此操作，如果未加载 SMB，此命令应输出 nothing：

```bash
lsmod | grep cifs
```

若要卸载该模块，请首先卸载所有 SMB 共享（使用上面所述的 `umount` 命令）。 可以通过以下命令标识系统上所有已装载的 SMB 共享：

```bash
mount | grep cifs
```

卸载所有 SMB 文件共享后，可以安全地卸载模块。 为此，可以使用 `modprobe` 命令：

```bash
sudo modprobe -r cifs
```

你可以使用 `modprobe` 命令，手动加载已卸载的 SMB 1 的模块：

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

最后，你可以通过查看 `/sys/module/cifs/parameters`中的加载参数来检查是否已使用参数加载 SMB 模块：

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

若要在 Ubuntu 和基于 Debian 的分发上永久禁用 SMB 1，则必须创建一个新文件（如果还没有其他模块的自定义选项），并在该设置中调用 `/etc/modprobe.d/local.conf`。 可以通过以下命令执行此操作：

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

可以通过加载 SMB 模块来验证此操作是否正常工作：

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>反馈
Linux 用户，我们希望倾听意见！

针对 Linux 用户组的 Azure 文件提供了一个论坛，在 Linux 上评估和采用文件存储时，可以在该论坛上共享反馈。 向 [Azure 文件 Linux 用户](mailto:azurefiles@microsoft.com)发送电子邮件可加入该用户组。

## <a name="next-steps"></a>后续步骤
请参阅以下链接，获取有关 Azure 文件的更多信息：

* [规划 Azure 文件部署](storage-files-planning.md)
* [常见问题](../storage-files-faq.md)
* [故障排除](storage-troubleshoot-linux-file-connection-problems.md)
