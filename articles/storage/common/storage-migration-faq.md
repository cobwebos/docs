---
title: Azure 存储迁移常见问题解答 | Microsoft 文档
description: 有关迁移 Azure 存储的常见问题解答
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: b020cbd15ba17609b7a476ccda9b1e814405341e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858775"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>有关 Azure 存储迁移的常见问题

本文回答了有关 Azure 存储迁移的常见问题。

## <a name="copy-upload-or-download"></a>复制、上传或下载

如何创建一个脚本来将文件从一个容器复制到另一个容器？ 

若要在容器之间复制文件，可以使用 AzCopy。 请参阅以下示例：

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy 将使用[复制 Blob API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 来复制容器中的每个文件。  

你可以使用任何具有 Internet 访问权限的虚拟机或本地计算机来运行 AzCopy。 你还可以使用 Azure Batch 计划自动执行此操作，但它更复杂一些。  

自动化脚本旨在用于 Azure 资源管理器部署而不是存储内容操作。 有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)。

**在相同区域内同一个存储帐户上的两个文件共享之间复制数据是否有任何费用？**

否。 此过程不会产生任何费用。

**如何从 Azure 门户下载 1-2 TB 的数据？**

请使用 AzCopy 下载数据。 有关详细信息，请参阅[使用 AzCopy on Windows 传输数据](storage-use-azcopy.md)和[使用 AzCopy on Linux 传输数据](storage-use-azcopy-linux.md)。

**除了使用门户中的下载选项，还有什么方法可以将 VHD 下载到本地计算机？**

你可以使用[存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)下载 VHD。

如何将数据从 Azure 存储帐户下载到基于 Linux 的计算机，或者从 Linux 计算机上传数据？ 

你可以使用 Azure CLI。

- 下载单个 blob：

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- 上传单个 Blob：

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**如何将 Blob 从一个存储帐户复制到另一个存储帐户？**

 可以使用我们的 [Blob 迁移脚本](../scripts/storage-common-transfer-between-storage-accounts.md)。
 
## <a name="migration-or-backup"></a>迁移或备份

如何将数据从一个存储容器复制到另一个？ 

执行以下步骤：

1.  在目标 blob 中创建容器（文件夹）。

2.  使用 [Azcopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) 将原始 Blob 容器中的内容复制到不同的 Blob 容器。

**如何实现创建 PowerShell 脚本以将数据从一个 Azure 文件共享移动到 Azure 存储中的另一个？**

使用 AzCopy 将数据从一个 Azure 文件共享移动到 Azure 存储中的另一个。 有关详细信息，请参阅[使用 AzCopy on Windows 传输数据](storage-use-azcopy.md)和[使用 AzCopy on Linux 传输数据](storage-use-azcopy-linux.md)。

**如何实现将大型 .csv 文件上传到 Azure 存储？**

使用 AzCopy 将大型 .csv 文件上传到 Azure 存储。 有关详细信息，请参阅[使用 AzCopy on Windows 传输数据](storage-use-azcopy.md)和[使用 AzCopy on Linux 传输数据](storage-use-azcopy-linux.md)。

**我必须每天将驱动器 D 中的日志移动到我的 Azure 存储帐户。** 如何自动执行该操作？

你可以使用 AzCopy，并在任务计划程序中创建任务。 使用 AzCopy 批处理脚本将文件上传到 Azure 存储帐户。 有关详细信息，请参阅[如何配置和运行云服务的启动任务](../../cloud-services/cloud-services-startup-tasks.md)。

如何在订阅之间移动存储帐户？ 

使用 AzCopy 在订阅之间移动存储帐户。 有关详细信息，请参阅[使用 AzCopy on Windows 传输数据](storage-use-azcopy.md)和[使用 AzCopy on Linux 传输数据](storage-use-azcopy-linux.md)。

如何将大约 10 TB 的数据移动到另一个区域的存储？ 

请使用 AzCopy 移动数据。 有关详细信息，请参阅[使用 AzCopy on Windows 传输数据](storage-use-azcopy.md)和[使用 AzCopy on Linux 传输数据](storage-use-azcopy-linux.md)。

**如何将数据从本地复制到 Azure 存储？**

请使用 AzCopy 复制数据。 有关详细信息，请参阅[使用 AzCopy on Windows 传输数据](storage-use-azcopy.md)和[使用 AzCopy on Linux 传输数据](storage-use-azcopy-linux.md)。

**如何将数据从本地复制到 Azure 文件？**

请使用 AzCopy 移动数据。 有关详细信息，请参阅[使用 AzCopy on Windows 传输数据](storage-use-azcopy.md)和[使用 AzCopy on Linux 传输数据](storage-use-azcopy-linux.md)。

如何将托管磁盘移动到另一个存储帐户？ 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

执行以下步骤：

1.  停止托管磁盘附加到的虚拟机。

2.  通过运行以下 Azure PowerShell 脚本，将托管磁盘 VHD 从一个区域复制到另一个区域：

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  通过在复制了 VHD 的其他区域中使用 VHD 文件来创建托管磁盘。 为此，请运行以下 Azure PowerShell 脚本：  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ```

有关如何从托管磁盘部署虚拟机的详细信息，请参阅 [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1)。

**如何从存储帐户移动或下载数据？**

请使用 AzCopy 下载数据。 有关详细信息，请参阅[使用 AzCopy on Windows 传输数据](storage-use-azcopy.md)和[使用 AzCopy on Linux 传输数据](storage-use-azcopy-linux.md)。

**如何从高级存储帐户移动到标准存储帐户？**

执行以下步骤：

1.  创建标准存储帐户。 （或在订阅中使用现有标准存储帐户。）

2.  下载 AzCopy。 请运行以下的 AzCopy 命令之一。

    在存储帐户中复制整个磁盘：

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    要仅复制一个磁盘，请在 Pattern 中提供磁盘名称  ：

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

完成该操作可能需花费数小时。

为确保传输成功完成，请检查 Azure 门户中的目标存储帐户容器。 将磁盘复制到标准存储帐户后，可以将其作为现有磁盘附加到虚拟机。 有关详细信息，请参阅[如何在 Azure 门户中将托管数据磁盘附加到 Windows 虚拟机](../../virtual-machines/windows/attach-managed-disk-portal.md)。  

**如何实现从经典存储帐户迁移到 Azure 资源管理器存储帐户？**

可以使用 Move-AzureStorageAccount cmdlet  。 此 cmdlet 有多个步骤（验证、准备、提交）。 移动之前，可先进行验证。

如果有虚拟机，则在迁移存储帐户数据之前，还需要执行其他步骤。 有关详细信息，请参阅[使用 Azure PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md)。

**如何实现将整个存储帐户备份到其他存储帐户？**

没有直接备份整个存储帐户的选项。 但你可以使用 AzCopy 或 Azure 存储资源管理器手动将该存储帐户中的容器移到另一个帐户。 以下步骤演示如何使用 AzCopy 移动容器：  

1.  安装 [AzCopy](storage-use-azcopy.md) 命令行工具。 此工具可帮助你在存储帐户间移动 VHD 文件。

2.  使用安装程序在 Windows 上安装 AzCopy 之后，打开命令提示符窗口，然后浏览到计算机上的 AzCopy 安装文件夹。 默认情况下，AzCopy 安装到 **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** 或 **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**。

3.  要移动容器，请运行以下命令。 必须使用实际值替换文本。   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`：提供源存储帐户的 URI（直至容器）。  
    - `/Dest`：提供目标存储帐户的 URI（直至容器）。  
    - `/SourceKey`：提供源存储帐户的主密钥。 可通过选择存储帐户从 Azure 门户复制此键值。  
    - `/DestKey`：提供目标存储帐户的主密钥。 可通过选择存储帐户从门户复制此键值。

运行此命令后，容器文件会移到目标存储帐户。

> [!NOTE]
> 从一个 Azure blob 复制到另一个时，AzCopy CLI 不能与 Pattern 开关一起使用  。
>
> 可以直接复制和编辑 AzCopy cmd，然后交叉检查以确保 Pattern 与源匹配  。 此外，请确保 /S  通配符有效。 有关详细信息，请参阅 [AzCopy 参数](storage-use-azcopy.md)。

如何备份 Azure 文件存储？ 

没有任何备份解决方案。 但是 Azure 文件也支持异步复制。 因此，可复制文件：

- 在一个存储帐户中从一个共享复制到另一个共享或复制到不同存储帐户。

- 在一个存储帐户中从一个共享复制到一个 Blob 容器或复制到不同存储帐户。

有关详细信息，请参阅[使用 AzCopy on Windows 传输数据](storage-use-azcopy.md)。
## <a name="configuration"></a>配置

如何将存储帐户的辅助位置更改为欧洲区域？****

创建存储帐户时，可以为帐户选择主要区域。 选择次要区域是根据主要区域确定的且无法更改。 有关详细信息，请参阅[异地冗余存储 (GRS)：Azure 存储的跨区域复制](storage-redundancy.md)。

我在哪里可以获得有关 Azure 存储服务加密 (SSE) 的更多信息？   

请参阅以下文章：

-  [Azure 存储安全指南](../blobs/security-recommendations.md)

-  [静态数据的 Azure 存储服务加密](storage-service-encryption.md)

如何在存储帐户中加密数据？ 

在存储帐户中启用加密后，不加密现有数据。 若要加密现有数据，必须再次将数据上传到存储帐户。

使用 AzCopy 将数据复制到不同的存储帐户，然后移回数据。 你还可以使用[静态加密](storage-service-encryption.md)。

**将存储帐户的复制从异地冗余存储更改到本地冗余存储是否有先决条件？**

否。

**如何转换到 Azure 高级存储以共享文件？**

Azure 文件共享上不允许使用高级存储。

**如何从标准存储帐户升级到高级存储帐户？如何实现从高级存储帐户降级到标准存储帐户？**

必须创建目标存储帐户，将数据从源帐户复制到目标帐户，然后删除源帐户。 可使用 AzCopy 等工具复制数据。

如果有虚拟机，则在迁移存储帐户数据之前，还需要执行其他步骤。 有关详细信息，请参阅[迁移到 Azure 高级存储（非托管磁盘）](storage-migration-to-premium-storage.md)。

怎样才能让其他人访问我的存储资源？ 

让其他人访问存储资源：

-   使用共享访问签名 (SAS) 令牌提供资源的访问权限。

-   向用户提供存储帐户的主密钥或辅助密钥。 有关详细信息，请参阅[管理存储帐户访问密钥](storage-account-keys-manage.md)。

-   更改访问策略以允许匿名访问。 有关详细信息，请参阅[授予对容器和 Blob 的匿名用户权限](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)。

AzCopy 安装在什么位置？ 

-   如果从 Microsoft Azure 存储命令行访问 AzCopy，请键入 AzCopy****。 命令行与 AzCopy 一起安装。

-   如果你安装的是 32 位版本，它将位于：%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy  。

-   如果你安装的是 64 位版本，它将位于：%ProgramFiles%\\Microsoft SDKs\\Azure\\AzCopy  。

**如何实现将 HTTPS 自定义域与我的存储帐户一起使用？例如，如何使 "https：\//mystorageaccountname.blob.core.windows.net/images/image.gif" 显示为 "https：\//www.contoso.com/images/image.gif"？**

具有自定义域的存储帐户当前不支持 TLS/SSL。
但你可以使用非 HTTPS 自定义域。 有关详细信息，请参阅[为 Blob 存储终结点配置自定义域名](../blobs/storage-custom-domain-name.md)。

## <a name="access-to-storage"></a>访问存储

如何在虚拟机上映射容器文件夹？ 

使用 Azure 文件共享。

如何访问 Azure 文件冗余存储？ 

读取访问异地冗余存储需要访问冗余存储。 但是，Azure 文件仅支持本地冗余存储和不允许只读访问的标准异地冗余存储。

**对于复制的存储帐户（如区域冗余存储、异地冗余存储或读取访问异地冗余存储），如何访问存储在次要区域中的数据？**

-   如果使用的是区域冗余存储空间或异地冗余存储，除非对该区域启动故障转移，否则无法从次要区域访问数据。 有关故障转移过程的详细信息，请参阅[灾难恢复和存储帐户故障转移](storage-disaster-recovery-guidance.md)。

-   如果使用的是读取访问异地冗余存储，可以随时从次要区域访问数据。 使用以下方法之一：  

    - **AzCopy**：在 URL 中的存储帐户名后追加 -secondary 以访问辅助终结点  。 例如：  

      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS 令牌**：使用 SAS 令牌访问终结点中的数据。 有关详细信息，请参阅[使用共享访问签名](storage-sas-overview.md)。

**如何使用 FTP 访问存储帐户中的数据？**

使用 FTP 无法直接访问存储帐户。 但是，你可以设置 Azure 虚拟机，然后在虚拟机上安装 FTP 服务器。 可让 FTP 服务器将文件存储在 Azure 文件共享上或虚拟机可用的数据磁盘上。

如果你只想下载数据而不需要使用存储资源管理器或类似的应用程序，则可以使用 SAS 令牌。 有关详细信息，请参阅[使用共享访问签名](storage-sas-overview.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 请联系支持人员。

如果仍需要帮助，可 [联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 来快速解决问题。
