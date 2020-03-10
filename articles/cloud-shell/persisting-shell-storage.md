---
title: 在 Azure Cloud Shell 中持久保存文件 |Microsoft Docs
description: 演练 Azure Cloud Shell 如何持久保存文件。
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: 15a5770eb2964f0f2039fe93de904af65d4c81ed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360171"
---
# <a name="persist-files-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中持久保存文件
Cloud Shell 利用 Azure 文件存储在会话之间持久保存文件。 初始启动时，Cloud Shell 会提示关联新的或现有的文件共享，以便在会话之间持久保存文件。

> [!NOTE]
> Bash 和 PowerShell 共享相同的文件共享。 只能有一个文件共享与 Cloud Shell 中的自动装载相关联。

> [!NOTE]
> Cloud shell 存储帐户不支持 Azure 存储防火墙。

## <a name="create-new-storage"></a>创建新存储

使用基本设置并仅选择一个订阅时，Cloud Shell 会在最靠近你的支持区域中代表你创建三个资源：
* 资源组：`cloud-shell-storage-<region>`
* 存储帐户：`cs<uniqueGuid>`
* 文件共享：`cs-<user>-<domain>-com-<uniqueGuid>`

![订阅设置](media/persisting-shell-storage/basic-storage.png)

文件共享在 `clouddrive` 目录中装载为 `$Home`。 这是一次性操作，文件共享会自动装载在后续会话中。 

文件共享还包含一个为你创建的 5-GB 映像，该映像自动将数据持久保存在 `$Home` 目录中。 对于 Bash 和 PowerShell 都是这样。

## <a name="use-existing-resources"></a>使用现有资源

通过使用高级选项，可以将现有资源相关联。 选择 Cloud Shell 区域时，必须选择位于同一区域的后备存储帐户。 例如，如果分配的区域是美国西部，则还必须将位于美国西部的文件共享相关联。

出现存储设置的提示时，选择“显示高级设置”查看其他选项。 填充的存储选项针对本地冗余存储 (LRS)、异地冗余存储 (GRS) 和区域冗余存储 (ZRS) 帐户进行筛选。 

> [!NOTE]
> 建议使用 GRS 或 ZRS 存储帐户以提高后备文件共享的复原能力。 选择哪种类型的冗余取决于你的目标和价格首选项。 [详细了解 Azure 存储帐户的复制选项](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。

![资源组设置](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>保护存储访问
安全起见，每个用户应预配自己的存储帐户。  对于基于角色的访问控制 (RBAC)，用户必须具有存储帐户级别的“参与者”访问权限或更高访问权限。

Cloud Shell 在指定订阅中的存储帐户内使用 Azure 文件共享。 由于继承了权限，对订阅拥有足够访问权限的用户将能够访问订阅中包含的所有存储帐户和文件共享。

用户应通过在存储帐户或订阅级别设置权限来锁定对其文件的访问权限。

## <a name="supported-storage-regions"></a>支持的存储区域
若要查找当前区域，可在 Bash 中运行 `env`，并 `ACC_LOCATION`或 PowerShell run `$env:ACC_LOCATION`中找到该变量。 文件共享会收到系统创建的 5-GB 映像，用于保存 `$Home` 目录。

Cloud Shell 计算机位于以下区域中：

|区域|区域|
|---|---|
|美洲|美国东部、美国中南部、美国西部|
|欧洲|欧洲北部、欧洲西部|
|亚太区|印度中部、亚洲东南部|

客户应选择主要区域，除非他们要求静态数据存储在特定区域中。 如果有这样的要求，则应使用辅助存储区域。

### <a name="secondary-storage-regions"></a>辅助存储区域
如果使用了辅助存储区域，则关联的 Azure 存储帐户与要将其装载到的 Cloud Shell 计算机位于不同的区域中。 例如，Jane 可以将其存储帐户设置为位于加拿大东部的次要区域中，但她装载到的计算机仍位于主要区域。 她的静态数据位于加拿大，但在美国中进行处理。

> [!NOTE]
> 如果使用了次要区域，则 Cloud Shell 的文件访问和启动时间可能会更慢。

用户可以在 PowerShell 中运行 `(Get-CloudDrive | Get-AzStorageAccount).Location` 以查看其文件共享的位置。

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>根据 Azure 资源策略限制资源创建
在 Cloud Shell 中创建的存储帐户都标记有 `ms-resource-usage:azure-cloud-shell`。 如果想禁止用户在 Cloud Shell 中创建存储帐户，请创建此特定标记触发的[适用于标记的 Azure 资源策略](../azure-policy/json-samples.md)。

## <a name="how-cloud-shell-storage-works"></a>Cloud Shell 存储的工作原理 
Cloud Shell 通过以下两种方法持久保存文件： 
* 创建 `$Home` 目录的磁盘映像来持久保持目录中所有内容。 磁盘映像将作为 `acc_<User>.img` 保存在指定的文件共享中，位于以下位置：`fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`，并会自动同步更改。 
* 将指定的文件共享装载为 `clouddrive` 目录中的 `$Home` 以便直接进行文件共享交互。 `/Home/<User>/clouddrive` 映射到 `fileshare.storage.windows.net/fileshare`。
 
> [!NOTE]
> `$Home` 目录中的所有文件（如 SSH 密钥）将持久保存用户磁盘映像（存储于已装载的文件共享中）中。 在 `$Home` 目录和已装载的文件共享中持久保存信息时，请应用最佳做法。

## <a name="clouddrive-commands"></a>clouddrive 命令

### <a name="use-the-clouddrive-command"></a>使用 `clouddrive` 命令
在 Cloud Shell 中，您可以运行名为 `clouddrive`的命令，这样您就可以手动更新装载到 Cloud Shell 的文件共享。
![运行“clouddrive”命令](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>列出 `clouddrive`
若要查明哪些文件共享已装载为 `clouddrive`，请运行 `df` 命令。 

clouddrive 的文件路径会在 URL 中显示存储帐户名称和文件共享。 例如： `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>装载新的 clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>手动装载的先决条件
可使用 `clouddrive mount` 命令更新与 Cloud Shell 相关联的文件共享。

如果装载现有的文件共享，则存储帐户必须位于所选的 Cloud Shell 区域中。 通过运行 `env` 并检查 `ACC_LOCATION`来检索位置。

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` 命令

> [!NOTE]
> 装载新的文件共享时，将为 `$Home` 目录创建新的用户映像。 以前的 `$Home` 映像保留在以前的文件共享中。

使用以下参数运行 `clouddrive mount` 命令：

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

若要查看更多详细信息，请运行 `clouddrive mount -h`，如下所示：

![运行 `clouddrive mount` 命令](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>卸载 clouddrive
随时可以卸载已装载到 Cloud Shell 的文件共享。 由于 Cloud Shell 需要使用已装载的文件共享，因此，在下一个会话中，系统会提示创建并装载另一个文件共享。

1. 运行 `clouddrive unmount`。
2. 确认提示。

除非手动删除，否则文件共享将一直存在。 Cloud Shell 在后续会话中不会再搜索此文件共享。 若要查看更多详细信息，请运行 `clouddrive unmount -h`，如下所示：

![运行 `clouddrive unmount` 命令](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> 虽然运行此命令不会删除任何资源，但手动删除映射到 Cloud Shell 的资源组、存储帐户或文件共享会清除 `$Home` 目录磁盘映像和文件共享中的任何文件。 此操作不可撤消。
## <a name="powershell-specific-commands"></a>特定于 PowerShell 的命令

### <a name="list-clouddrive-azure-file-shares"></a>列出 `clouddrive` Azure 文件共享
`Get-CloudDrive` cmdlet 检索 `clouddrive` 当前在 Cloud Shell 中装载的 Azure 文件共享信息。 <br>
![运行 Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>卸载 `clouddrive`
随时可以卸载已装载到 Cloud Shell 的 Azure 文件共享。 如果删除了 Azure 文件共享，则在下一个会话中，系统会提示创建并装载一个新的 Azure 文件共享。

`Dismount-CloudDrive` cmdlet 从当前存储帐户中卸载 Azure 文件共享。 卸载 `clouddrive` 会终止当前会话。 在下一次会话期间，系统会提示用户创建并装载新的 Azure 文件共享。
![运行 Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

注意：如果需要在文件中定义一个函数并从 PowerShell cmdlet 中调用该函数，则必须包含点运算符。 例如： .\MyFunctions.ps1

## <a name="next-steps"></a>后续步骤
[Cloud Shell 快速入门](quickstart.md) <br>
[了解 Microsoft Azure 文件存储](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[了解存储标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
