---
title: 使用 NFS 3.0 协议装载 Azure Blob 存储 (预览) |Microsoft Docs
description: 了解如何从 Azure 虚拟机 (VM) 或使用 NFS 3.0 协议本地运行的客户端在 Blob 存储中装载容器。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 2517a0ac8edf30ac041708a57b166af6eb36440a
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760782"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>使用网络文件系统 (NFS) 3.0 协议 (预览中装载 Blob 存储) 

可以通过基于 Windows 或 Linux 的 Azure 虚拟机 (VM) 或使用 NFS 3.0 协议在本地运行的 Windows 或 Linux 系统，在 Blob 存储中装载容器。 本文提供了分步指南。 若要了解有关 Blob 存储中的 NFS 3.0 协议支持的详细信息，请参阅[Azure Blob 存储中的网络文件系统 (nfs) 3.0 协议支持 (预览) ](network-file-system-protocol-support.md)。

> [!NOTE]
> Azure Blob 存储中的 NFS 3.0 协议支持提供公共预览版，并在以下区域提供：美国东部、美国中部和加拿大中部。

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>步骤1：将 NFS 3.0 协议功能注册到你的订阅

1. 打开 PowerShell 命令窗口。 

2. 运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

   ```powershell
   Connect-AzAccount
   ```

3. 如果你的标识与多个订阅相关联，请设置你的活动订阅。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

4. `AllowNFSV3`使用以下命令注册此功能。

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. `PremiumHns`同时使用以下命令注册该功能。

   ```powershell
   Register-AzProviderFeature -FeatureName PremiumHns -ProviderNamespace Microsoft.Storage  
   ```

6. 使用以下命令注册资源提供程序。
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>步骤2：验证功能是否已注册 

注册批准最多可能需要一小时。 若要验证注册是否完成，请使用以下命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName PremiumHns  
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>步骤3：创建 Azure 虚拟网络 (VNet) 

存储帐户必须包含在 VNet 中。 VNet 使客户端可以安全地连接到你的存储帐户。 若要详细了解 VNet 以及如何创建 VNet，请参阅[虚拟网络文档](https://docs.microsoft.com/azure/virtual-network/)。

> [!NOTE]
> 同一 VNet 中的客户端可以在你的帐户中装载容器。 你还可以从在本地网络中运行的客户端装载容器，但必须先将本地网络连接到 VNet。 请参阅[支持的网络连接](network-file-system-protocol-support.md#supported-network-connections)。

## <a name="step-4-configure-network-security"></a>步骤4：配置网络安全

保护帐户中数据的唯一方法是使用 VNet 和其他网络安全设置。 用于保护数据的任何其他工具（包括帐户密钥授权、Azure Active Directory (AD) 安全性和访问控制列表 () Acl）在其上启用了 NFS 3.0 协议支持的帐户中不受支持。 

若要保护帐户中的数据，请参阅以下建议： [Blob 存储的网络安全建议](security-recommendations.md#networking)。

## <a name="step-5-create-and-configure-a-storage-account"></a>步骤5：创建和配置存储帐户

若要使用 NFS 3.0 装载容器，必须在将该功能注册到订阅**后**创建存储帐户。 你无法启用在注册该功能之前已存在的帐户。 

在此功能的预览版本中，仅[BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md)帐户支持 NFS 3.0 协议。

配置帐户时，请选择以下值：

|设置 | 值|
|----|---|
|位置|以下区域之一：美国东部、美国中部和加拿大中部 |
|性能|高级|
|帐户类型|BlockBlobStorage|
|复制|本地冗余存储 (LRS)|
|连接方法|公共终结点 (所选网络) 或专用终结点|
|需要安全传输|已禁用|
|分层命名空间|已启用|
|NFS V3|已启用|

您可以接受所有其他设置的默认值。 

## <a name="step-6-create-a-container"></a>步骤 6：创建容器

使用以下任意工具或 Sdk 在存储帐户中创建容器：

|工具|SDK|
|---|---|
|[Azure 存储资源管理器](data-lake-storage-explorer.md#create-a-container)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
|[Azure 门户](https://portal.azure.com)|[REST](https://docs.microsoft.com/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>步骤7：装载容器

在 Windows 或 Linux 系统上创建一个目录，然后在存储帐户中装载一个容器。

### <a name="linux"></a>[Linux](#tab/linux)

1. 在 Linux 系统上，创建一个目录。

   ```
   mkdir -p /mnt/test
   ```

2. 使用以下命令装载容器。

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - 将 `<storage-account-name>` 此命令中出现的占位符替换为你的存储帐户的名称。  

   - 将 `<container-name>` 占位符替换为你的容器的名称。


### <a name="windows"></a>[Windows](#tab/windows)

1. 打开 " **Windows 功能**" 对话框，并打开 " **NFS 客户端**" 功能。 

   ![网络文件系统客户端功能](media/network-file-system-protocol-how-to/client-for-network-files-system-feature.png)

2. 使用[mount](https://docs.microsoft.com/windows-server/administration/windows-commands/mount)命令装载容器。

   ```
   mount -o nolock <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name> *
   ```

   - 将 `<storage-account-name>` 此命令中出现的占位符替换为你的存储帐户的名称。  

   - 将 `<container-name>` 占位符替换为你的容器的名称。

---

## <a name="resolve-common-issues"></a>解决常见问题

|问题/错误 | 解决方法|
|---|---|
|`Access denied by server while mounting`|请确保客户端在支持的子网中运行。 请参阅[支持的网络位置](network-file-system-protocol-support.md#supported-network-connections)。|
|`No such file or directory`| 请确保在验证功能已注册后创建要装载的容器。 请参阅[步骤2：验证是否已注册该功能](#step-2-verify-that-the-feature-is-registered)。另外，请确保将 mount 命令和它的参数直接输入到终端中。 如果你将该命令的任何部分从另一个应用程序复制并粘贴到终端，粘贴的信息中的隐藏字符可能会导致出现此错误。|

## <a name="see-also"></a>另请参阅

[Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持 (预览) ](network-file-system-protocol-support.md)







