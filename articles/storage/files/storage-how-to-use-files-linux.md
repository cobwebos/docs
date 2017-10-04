---
title: "通过 Linux 使用 Azure 文件 | Microsoft Docs"
description: "了解如何在 Linux 上通过 SMB 装载 Azure 文件共享。"
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6c80c6814dacf90a0a50dab691899c3a8cc2bf82
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="use-azure-files-with-linux"></a>通过 Linux 使用 Azure 文件
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 可以使用 [Samba 项目](https://www.samba.org/)中的 [cifs-utils 包](https://wiki.samba.org/index.php/LinuxCIFS_utils)在 Linux 分发版中装载 Azure 文件共享。 本文介绍装载 Azure 文件共享的两种方法：使用 `mount` 命令按需装载，以及通过在 `/etc/fstab` 中创建一个条目在启动时装载。

> [!NOTE]  
> 若要将 Azure 文件共享装载到其被托管时所在的 Azure 区域之外（例如本地或其他 Azure 区域），OS 必须支持 SMB 3.0 的加密功能。 4.11 内核中引入了适用于 Linux 的 SMB 3.0 加密功能。 使用此功能可从本地或不同 Azure 区域装载 Azure 文件共享。 发布时，此功能已向后移植到 Ubuntu 16.04 和更高版本。


## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>使用 Linux 和 cifs-utils 包装载 Azure 文件共享的先决条件
* **选择已安装 cifs-utils 包的 Linux 分发版**：Microsoft 建议选择 Azure 映像库中的以下 Linux 分发版：

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>已安装 cifs-utils 包：可在所选的 Linux 分发版上使用程序包管理器安装 cifs-utils。 

    在 **Ubuntu** 和**基于 Debian** 的分发版上，请使用 `apt-get` 包管理器：

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    在 **RHEL** 和 **CentOS** 上，请使用 `yum` 包管理器：

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    在 **openSUSE** 上，请使用 `zypper` 包管理器：

    ```
    sudo zypper install samba*
    ```

    在其他分发版上，请使用相应的包管理器，或[从源编译](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)。

* **确定已装载共享的目录/文件权限**：在以下示例中，我们使用 0777 向所有用户授予读取、写入和执行权限。 可以根据需要将这些权限替换为其他 [chmod 权限](https://en.wikipedia.org/wiki/Chmod)。 

* 存储帐户名称：需提供存储帐户的名称才能装载 Azure 文件共享。

* 存储帐户密钥：需提供主要（或辅助）存储密钥才能装载 Azure 文件共享。 目前不支持使用 SAS 密钥进行装载。

* **确保已打开端口 445**：SMB 通过 TCP 端口 445 通信 - 请查看防火墙是否未阻止 TCP 端口 445 与客户端计算机通信。

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>使用 `mount` 按需装载 Azure 文件共享
1. [安装适用于 Linux 分发版的 cifs-utils 包](#install-cifs-utils)。

2. **为装入点创建一个文件夹**：可在文件系统上的任何位置执行此操作。

    ```
    mkdir mymountpoint
    ```

3. **使用 mount 命令装载 Azure 文件共享**：记得将 `<storage-account-name>`、`<share-name>` 和 `<storage-account-key>` 替换为适当的信息。

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=2.1,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> 使用完 Azure 文件共享后，可以使用 `sudo umount ./mymountpoint` 卸载共享。

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>使用 `/etc/fstab` 为 Azure 文件共享创建持久装入点
1. [安装适用于 Linux 分发版的 cifs-utils 包](#install-cifs-utils)。

2. **为装入点创建一个文件夹**：可在文件系统上的任何位置执行此操作，但需要记下该文件夹的绝对路径。 以下示例在根目录下创建一个文件夹。

    ```
    sudo mkdir /mymountpoint
    ```

3. **使用以下命令将以下代码行追加到 `/etc/fstab`**：记得将 `<storage-account-name>`、`<share-name>` 和 `<storage-account-key>` 替换为适当的信息。

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=2.1,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> 编辑 `/etc/fstab` 后，可以使用 `sudo mount -a` 装载 Azure 文件共享，而无需重启。

## <a name="feedback"></a>反馈
Linux 用户，我们希望倾听意见！

针对 Linux 用户组的 Azure 文件提供了一个论坛，在 Linux 上评估和采用文件存储时，可以在该论坛上共享反馈。 向 [Azure 文件 Linux 用户](mailto:azurefileslinuxusers@microsoft.com)发送电子邮件可加入该用户组。

## <a name="next-steps"></a>后续步骤
请参阅以下链接，获取有关 Azure 文件的更多信息。
* [文件服务 REST API 参考](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [如何对 Microsoft Azure 存储使用 AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [对 Azure 存储使用 Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [常见问题](../storage-files-faq.md)
* [故障排除](storage-troubleshoot-linux-file-connection-problems.md)

