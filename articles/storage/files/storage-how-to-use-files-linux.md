---
title: 通过 Linux 使用 Azure 文件 | Microsoft Docs
description: 了解如何在 Linux 上通过 SMB 装载 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fcc9876caf0c002650ab30b7eaed7dc44e2f135e
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137733"
---
# <a name="use-azure-files-with-linux"></a>通过 Linux 使用 Azure 文件
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 可以使用 [SMB 内核客户端](https://wiki.samba.org/index.php/LinuxCIFS)在 Linux 分发版中装载 Azure 文件共享。 本文介绍装载 Azure 文件共享的两种方法：使用 `mount` 命令按需装载，以及通过在 `/etc/fstab` 中创建一个条目在启动时装载。

在 Linux 上装载 Azure 文件共享的建议方法是使用 SMB 3.0。 默认情况下，Azure 文件存储要求进行传输中加密，而只有 SMB 3.0 支持传输中加密。 Azure 文件存储也支持 SMB 2.1，但后者不支持传输中加密，同时出于安全原因，不可以使用 SMB 2.1 从另一 Azure 区域或本地装载 Azure 文件共享。 除非应用程序专门需要用到 SMB 2.1，否则几乎没有任何理由使用它，因为最近发布的最流行 Linux 分发版都支持 SMB 3.0：  

| | SMB 2.1 <br>（装载在同一 Azure 区域内的 VM 上） | SMB 3.0 <br>（从本地和跨区域装载） |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

如果使用的是上表中未列出的 Linux 分发版，可以通过检查 Linux 内核版本，来确定所用 Linux 分发版是否支持提供加密功能的 SMB 3.0。 提供加密功能的 SMB 3.0 已添加到 Linux 内核版本 4.11。 `uname` 命令将返回所用 Linux 内核的版本：

```bash
uname -r
```

## <a name="prerequisites"></a>先决条件
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**确保已安装 cifs-utils 包。**  
    可在所选的 Linux 分发版上使用包管理器安装 cifs-utils 包。 

    在 **Ubuntu** 和**基于 Debian** 的分发版上，请使用 `apt` 包管理器：

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    在“Fedora”、“Red Hat Enterprise Linux 8+”和“CentOS 8+”中，请使用 `dnf` 包管理器：   

    ```bash
    sudo dnf install cifs-utils
    ```

    在旧版的“Red Hat Enterprise Linux”和“CentOS”中，请使用 `yum` 包管理器：  

    ```bash
    sudo yum install cifs-utils 
    ```

    在 **openSUSE** 上，请使用 `zypper` 包管理器：

    ```bash
    sudo zypper install cifs-utils
    ```

    在其他分发版上，请使用相应的包管理器，或[从源编译](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **最新版本的 Azure 命令行接口 (CLI)。** 若要详细了解如何安装 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 并选择操作系统。 如果你想要在 PowerShell 6+ 中使用 Azure PowerShell 模块，也可以使用，不过，下面的说明适用于 Azure CLI。

* **确保端口 445 处于打开状态**：SMB 通过 TCP 端口 445 通信 - 请查看防火墙是否未阻止 TCP 端口 445 与客户端计算机通信。  请替换 **<your-resource-group>** 和 **<your-storage-account>**
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

    如果连接成功，应会看到如下所示的输出：

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    如果无法在企业网络中打开端口 445，或者 ISP 阻止此类操作，可以使用 VPN 连接或 ExpressRoute 来解决端口 445 的相关问题。 有关详细信息，请参阅[直接访问 Azure 文件共享时的网络注意事项](storage-files-networking-overview.md)。

## <a name="mounting-azure-file-share"></a>装载 Azure 文件共享
若要在 Linux 分发版中使用 Azure 文件共享，必须创建一个充当 Azure 文件共享装入点的目录。 可以在 Linux 系统上的任何位置创建装入点，但是通用约定是在 /mnt 下创建此装入点。 创建装入点之后，使用 `mount` 命令访问 Azure 文件共享。

如果需要，可将同一个 Azure 文件共享装载到多个装入点。

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>使用 `mount` 按需装载 Azure 文件共享
1. **为装入点创建一个文件夹**：请将 `<your-resource-group>`、`<your-storage-account>` 和 `<your-file-share>` 替换为适合你的环境的信息。

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **使用装载命令来装载 Azure 文件共享**。 在以下示例中，本地 Linux 文件和文件夹权限默认为 0755，表示所有者拥有读取、写入和执行权限（基于文件/目录 Linux 所有者），所有者组中的用户拥有读取和执行权限，系统中的其他用户拥有读取和执行权限。 可以使用 `uid` 和 `gid` 装载选项来设置装入点的用户 ID 和组 ID。 还可根据需要使用 `dir_mode` 和 `file_mode` 来设置自定义权限。 有关如何设置权限的详细信息，请参阅维基百科上的[UNIX 数值表示法](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)。 

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
    > 上述装载命令装载 SMB 3.0。 如果你的 Linux 分发版不支持提供加密功能的 SMB 3.0，或者仅支持 SMB 2.1，则你只能从存储帐户所在的同一区域中的 Azure VM 进行装载。 若要在不支持提供加密功能的 SMB 3.0 的 Linux 分发版上装载 Azure 文件共享，需要[对存储帐户禁用传输中加密](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

使用完 Azure 文件共享后，可以使用 `sudo umount $mntPath` 卸载共享。

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>使用 `/etc/fstab` 为 Azure 文件共享创建持久装入点
1. **为装入点创建一个文件夹**：可在文件系统上的任何位置创建装入点的文件夹，但在/mnt。下创建该文件夹是常见约定。 例如，以下命令（请将 `<your-resource-group>`、`<your-storage-account>` 和 `<your-file-share>` 替换为适用于你的环境的信息）会创建一个新目录：

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

1. **使用以下命令将以下行追加到`/etc/fstab` **：在下面的示例中，本地 Linux 文件和文件夹权限默认为0755，这意味着所有者的读取、写入和执行（基于文件/目录 Linux 所有者），对所有者组中的用户进行读取和执行，并读取和执行系统中的其他用户。 可以使用 `uid` 和 `gid` 装载选项来设置装入点的用户 ID 和组 ID。 还可根据需要使用 `dir_mode` 和 `file_mode` 来设置自定义权限。 有关如何设置权限的详细信息，请参阅维基百科上的[UNIX 数值表示法](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)。

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
    > 上述装载命令装载 SMB 3.0。 如果你的 Linux 分发版不支持提供加密功能的 SMB 3.0，或者仅支持 SMB 2.1，则你只能从存储帐户所在的同一区域中的 Azure VM 进行装载。 若要在不支持提供加密功能的 SMB 3.0 的 Linux 分发版上装载 Azure 文件共享，需要[对存储帐户禁用传输中加密](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>使用 autofs 自动装载 Azure 文件共享

1. **确保安装了 autofs 包。**  

    可以在所选的 Linux 分发版上使用包管理器安装 autofs 包。 

    在 **Ubuntu** 和**基于 Debian** 的分发版上，请使用 `apt` 包管理器：
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    在“Fedora”、“Red Hat Enterprise Linux 8+”和“CentOS 8+”中，请使用 `dnf` 包管理器：************
    ```bash
    sudo dnf install autofs
    ```
    在旧版的“Red Hat Enterprise Linux”和“CentOS”中，请使用 `yum` 包管理器：********
    ```bash
    sudo yum install autofs 
    ```
    在 **openSUSE** 上，请使用 `zypper` 包管理器：
    ```bash
    sudo zypper install autofs
    ```
2. **为共享创建装入点**：
   ```bash
    sudo mkdir /fileshares
    ```
3. **创建新的自定义 autofs 配置文件**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **将以下条目添加到/etc/auto.fileshares**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **将以下条目添加到/etc/auto.master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **重新启动 autofs**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **访问为共享指定的文件夹**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>保护 Linux
若要在 Linux 上装载 Azure 文件共享，端口 445 必须可访问。 许多组织因 SMB 1 中固有的安全风险而阻止端口 445。 SMB 1（也称为通用 Internet 文件系统，简称 CIFS）是许多 Linux 分发版随附的一个传统文件系统协议。 SMB 1 是过时且效率不高的协议，而且最重要的是，它是不安全的协议。 好消息是 Azure 文件存储不支持 SMB 1，并且从 Linux 内核版本 4.18 开始，可在 Linux 中禁用 SMB 1。 我们始终[强烈建议](https://aka.ms/stopusingsmb1)在生产环境中使用 SMB 文件共享之前，禁用 Linux 客户端上的 SMB 1。

从 Linux 内核4.18 开始，为实现传统原因而调用`cifs`的 SMB 内核模块会公开名`disable_legacy_dialects`为的新模块参数（通常称为*parm* ）。 尽管 Linux 内核 4.18 中已引入此项更改，但某些供应商会将此项更改向后移植到他们支持的旧内核。 为方便起见，下表详细描述了此模块参数在常用 Linux 分发版上的可用性。

| 分发 | 可以禁用 SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | 否 |
| Ubuntu 18.04 | 是 |
| Ubuntu 19.04+ | 是 |
| Debian 8-9 | 否 |
| Debian 10+ | 是 |
| Fedora 29+ | 是 |
| CentOS 7 | 否 | 
| CentOS 8+ | 是 |
| Red Hat Enterprise Linux 6.x-7.x | 否 |
| Red Hat Enterprise Linux 8+ | 是 |
| openSUSE Leap 15.0 | 否 |
| openSUSE Leap 15.1+ | 是 |
| openSUSE Tumbleweed | 是 |
| SUSE Linux Enterprise 11.x-12.x | 否 |
| SUSE Linux Enterprise 15 | 否 |
| SUSE Linux Enterprise 15.1 | 否 |

可通过以下命令检查所用 Linux 分发版是否支持 `disable_legacy_dialects` 模块参数。

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

此命令应输出以下消息：

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

在禁用 SMB 1 之前，必须检查并确保 SMB 模块当前尚未加载到系统中（如果已装载 SMB 共享，则此模块会自动加载到系统中）。 可使用以下命令来执行此项检查。如果未加载 SMB，则此命令不会输出任何信息：

```bash
lsmod | grep cifs
```

若要卸载该模块，请先卸载所有 SMB 共享（使用上面所述的 `umount` 命令）。 可使用以下命令识别系统上所有已装载的 SMB 共享：

```bash
mount | grep cifs
```

卸载所有 SMB 文件共享后，可以安全地卸载该模块。 为此，可以使用 `modprobe` 命令：

```bash
sudo modprobe -r cifs
```

可以使用 `modprobe` 命令手动加载已卸载 SMB 1 的模块：

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

最后，可以通过查看 `/sys/module/cifs/parameters` 中加载的参数，来检查是否已使用相应参数加载了 SMB 模块：

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

若要在 Ubuntu 和基于 Debian 的分发版上永久禁用 SMB 1，必须创建一个名为 `/etc/modprobe.d/local.conf` 且包含相应设置的新文件（如果尚未对其他模块使用自定义选项）。 为此，可以使用以下命令：

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

可以通过加载 SMB 模块来验证上述操作是否成功：

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>后续步骤
请参阅以下链接，获取有关 Azure 文件的更多信息：

* [规划 Azure 文件部署](storage-files-planning.md)
* [常见问题解答](../storage-files-faq.md)
* [疑难解答](storage-troubleshoot-linux-file-connection-problems.md)
