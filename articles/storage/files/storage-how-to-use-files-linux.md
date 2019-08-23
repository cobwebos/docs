---
title: 通过 Linux 使用 Azure 文件 | Microsoft Docs
description: 了解如何在 Linux 上通过 SMB 装载 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9674df346ff0b5c046df26fe85bf7ba0bbb802e6
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900306"
---
# <a name="use-azure-files-with-linux"></a>通过 Linux 使用 Azure 文件

[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 可以使用 [SMB 内核客户端](https://wiki.samba.org/index.php/LinuxCIFS)在 Linux 分发版中装载 Azure 文件共享。 本文介绍装载 Azure 文件共享的两种方法：使用 `mount` 命令按需装载，以及通过在 `/etc/fstab` 中创建一个条目在启动时装载。

> [!NOTE]  
> 若要将 Azure 文件共享装载到其被托管时所在的 Azure 区域之外（例如本地或其他 Azure 区域），OS 必须支持 SMB 3.0 的加密功能。

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>使用 Linux 和 cifs-utils 包装载 Azure 文件共享的先决条件
<a id="smb-client-reqs"></a>

* **现有的 Azure 存储帐户和文件共享**：若要完成本文，需要创建一个存储帐户和文件共享。 如果尚未创建，请参阅有关该主题的快速入门之一：[创建文件共享 - CLI](storage-how-to-use-files-cli.md)

* **存储帐户名称和密钥**若要完成本文，需要提供存储帐户名称和密钥。 如果你参考 CLI 快速入门创建了一个存储帐户，则已经获得了这些凭据，否则，请查阅前面链接的 CLI 快速入门，了解如何检索存储帐户密钥。

* **选择 Linux 发行版以满足你的装载需求。**  
      可以通过 SMB 2.1 和 SMB 3.0 装载 Azure 文件。 对于来自本地或其他 Azure 区域中的客户端的连接，必须使用 SMB 3.0；Azure 文件会拒绝 SMB 2.1（或没有加密的 SMB 3.0）。 如果从同一个 Azure 区域内的 VM 访问 Azure 文件共享，则可使用 SMB 2.1 访问文件共享，当且仅当托管 Azure 文件共享的存储帐户禁用了“需要安全传输”时适用。 始终建议要求安全传输并仅使用带加密的 SMB 3.0。

    SMB 3.0 加密支持在 Linux 内核版本 4.11 中引入，已向后移植到常见 Linux 分发版的早期内核版本中。 在本文档发布时，Azure 库中的以下发行版支持表标题中指定的装载选项。 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>具有相应装载功能的最低建议版本（SMB 版本 2.1 与 SMB 版本 3.0）

|   | SMB 2.1 <br>（装载在同一 Azure 区域内的 VM 上） | SMB 3.0 <br>（从本地和跨区域装载） |
| --- | :---: | :---: |
| Ubuntu 服务器 | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 9 +<sup>*</sup> |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

<sup>*</sup>向后移植内核的使用可能是必需的。 可使用 `sudo apt-get install linux-image-cloud-amd64` 进行安装。

如果此处未列出你的 Linux 分发版，则你可以使用以下命令查看 Linux 内核版本：

```bash
uname -r
```

* <a id="install-cifs-utils"></a>**cifs-utils 包已安装。**  
    可在所选的 Linux 分发版上使用包管理器安装 cifs-utils 包。 

    在 **Ubuntu** 和**基于 Debian** 的分发版上，请使用 `apt-get` 包管理器：

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    在 **RHEL** 和 **CentOS** 上，请使用 `yum` 包管理器：

    ```bash
    sudo yum install cifs-utils
    ```

    在 **openSUSE** 上，请使用 `zypper` 包管理器：

    ```bash
    sudo zypper install cifs-utils
    ```

    在其他分发版上，请使用相应的包管理器，或[从源编译](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **确定已装载共享的目录/文件权限**：在以下示例中，权限 `0777` 用于向所有用户授予读取、写入和执行权限。 可以根据需要将其替换为其他 [chmod 权限](https://en.wikipedia.org/wiki/Chmod)，尽管这将意味着可能会限制访问权限。 如果使用其他权限，还应考虑使用 uid 和 gid，以便为所选的本地用户和组保留访问权限。

> [!NOTE]
> 如果未使用 dir_mode 和 file_mode 显式分配目录和文件权限，则它们将默认为 0755。

* **确保端口 445 处于打开状态**：SMB 通过 TCP 端口 445 通信 - 请查看防火墙是否未阻止 TCP 端口 445 与客户端计算机通信。

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>使用 `mount` 按需装载 Azure 文件共享

1. [安装适用于 Linux 分发版的 cifs-utils 包](#install-cifs-utils)。

1. **为装入点创建文件夹**：可以在文件系统上的任何位置创建装入点的文件夹，但是在新文件夹下创建此文件夹是惯例。 例如，使用以下命令可创建一个新目录，并将 **<storage_account_name>** 和 **<file_share_name>** 替换为适用于你的环境的信息：

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **使用 mount 命令装载 Azure 文件共享**：请记得将 **<storage_account_name>** 、 **<share_name>** 、 **<smb_version>** 、 **<storage_account_key>** 和 **<mount_point>** 替换为适用于你的环境的信息。 如果 Linux 分发版支持带加密的 SMB 3.0（有关详细信息，请参阅[了解 SMB 客户端要求](#smb-client-reqs)），请使用 **3.0** 作为 **<smb_version>** 。 对于不支持带加密的 SMB 3.0 的 Linux 分发版，请使用 **2.1** 作为 **<smb_version>** 。 只能使用 SMB 3.0 在 Azure 区域外部（包括本地或不同 Azure 区域中）装载 Azure 文件共享。 如果愿意，可以更改装载式共享的目录和文件权限，但是，这意味着会限制访问权限。

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> 使用完 Azure 文件共享后，可以使用 `sudo umount <mount_point>` 卸载共享。

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>使用 `/etc/fstab` 为 Azure 文件共享创建持久装入点

1. [安装适用于 Linux 分发版的 cifs-utils 包](#install-cifs-utils)。

1. **为装入点创建文件夹**：可以在文件系统上的任何位置创建装入点的文件夹，但是在新文件夹下创建此文件夹是惯例。 无论在何处创建此文件夹，请记下此文件夹的绝对路径。 例如，使用以下命令可创建一个新目录，并将 **<storage_account_name>** 和 **<file_share_name>** 替换为适用于你的环境的信息。

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **创建凭据文件以存储文件共享的用户名（存储帐户名称）和密码（存储帐户密钥）。** 将 **<storage_account_name>** 和 **<storage_account_key>** 替换为适用于你的环境的信息。

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **更改凭据文件的权限，以便只有 root 才能读取或修改密码文件。** 由于存储帐户密钥实质上是存储帐户的超级管理员密码，因此重要的是在文件上设置权限使只有 root 才能访问，这样较低权限的用户将无法检索存储帐户密钥。   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **使用以下命令将以下行追加到 `/etc/fstab`** ：请记得将 **<storage_account_name>** 、 **<share_name>** 、 **<smb_version>** 和 **<mount_point>** 替换为适用于你的环境的信息。 如果 Linux 分发版支持带加密的 SMB 3.0（有关详细信息，请参阅[了解 SMB 客户端要求](#smb-client-reqs)），请使用 **3.0** 作为 **<smb_version>** 。 对于不支持带加密的 SMB 3.0 的 Linux 分发版，请使用 **2.1** 作为 **<smb_version>** 。 只能使用 SMB 3.0 在 Azure 区域外部（包括本地或不同 Azure 区域中）装载 Azure 文件共享。

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs _netdev,nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> 编辑 `/etc/fstab` 后，可以使用 `sudo mount -a` 装载 Azure 文件共享，而无需重启。

## <a name="feedback"></a>反馈

Linux 用户，我们希望倾听意见！

针对 Linux 用户组的 Azure 文件提供了一个论坛，在 Linux 上评估和采用文件存储时，可以在该论坛上共享反馈。 向 [Azure 文件 Linux 用户](mailto:azurefileslinuxusers@microsoft.com)发送电子邮件可加入该用户组。

## <a name="next-steps"></a>后续步骤

请参阅以下链接，获取有关 Azure 文件的更多信息：

* [规划 Azure 文件部署](storage-files-planning.md)
* [常见问题](../storage-files-faq.md)
* [故障排除](storage-troubleshoot-linux-file-connection-problems.md)
