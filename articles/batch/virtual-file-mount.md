---
title: 在池上装载虚拟文件系统
description: 了解如何在 Batch 池上装载虚拟文件系统。
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/13/2019
ms.openlocfilehash: df03275fdeea88df1a2f2b6e2cda55021497cdf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89145478"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>在 Batch 池上装载虚拟文件系统

Azure Batch 现在支持在 Batch 池的 Windows 或 Linux 计算节点上装载云存储空间或外部文件系统。 计算节点加入池时，将装载虚拟文件系统并将其视为该节点上的本地驱动器。 可以装载文件系统，例如 Azure 文件存储、Azure Blob 存储、网络文件系统 (NFS)，包括 [Avere vFXT 缓存](../avere-vfxt/avere-vfxt-overview.md)或通用 Internet 文件系统 (CIFS)。

在本文中，你将了解如何使用[适用于 .NET 的 Batch 管理库](/dotnet/api/overview/azure/batch)在计算节点池上装载虚拟文件系统。

> [!NOTE]
> 2019-08-19 或之后创建的 Batch 池上支持装载虚拟文件系统。 2019-08-19 之前创建的 Batch 池不支持此功能。
> 
> 用于在计算节点上装载文件系统的 API 是 [Batch .NET](/dotnet/api/microsoft.azure.batch) 库的组成部分。

## <a name="benefits-of-mounting-on-a-pool"></a>装载在池上的好处

将文件系统装载到池中，而不是让任务从大型数据集中检索自己的数据，可以使任务能够更轻松且更高效地访问所需的数据。

考虑具有多个任务的场景，这些任务需要访问一组通用数据，例如渲染电影。 每个任务每次渲染场景文件中的一个或多个帧。 通过装载包含场景文件的驱动器，计算节点可以更轻松地访问共享数据。 此外，可以根据同时访问数据的计算节点数量所需的性能和规模（吞吐量和 IOPS）来单独选择基础文件系统以及对其进行缩放。 例如，可使用 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) 分布式内存中缓存，通过数千个并发渲染节点，支持大型动画规模级别的渲染，并访问驻留在本地的源数据。 另外，对于已经驻留在基于云的 Blob 存储中的数据，可以使用 [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) 将该数据装载为本地文件系统。 但 blobfuse 仅在 Linux 节点上可用，而 [Azure 文件存储](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/)提供了类似的工作流，并且在 Windows 和 Linux 上均可用。

## <a name="mount-a-virtual-file-system-on-a-pool"></a>在池上装载虚拟文件系统  

在池上装载虚拟文件系统可使文件系统用于池中的每个计算节点。 在计算节点加入池时，或节点重启或重置映像时，将配置文件系统。

要在池上装载文件系统，请创建 `MountConfiguration` 对象。 选择适合虚拟文件系统的对象：`AzureBlobFileSystemConfiguration`、`AzureFileShareConfiguration`、`NfsMountConfiguration` 或 `CifsMountConfiguration`。

所有装载配置对象都需要以下基本参数。 一些装载配置具有特定于所使用的文件系统的参数，本文将通过代码示例进行详细讨论。

- **帐户名或源**：需要提供存储帐户的名称或其源名称才能装载虚拟文件共享。
- **相对装载路径或源**：文件系统安装在计算节点上的位置，相对于可通过 `AZ_BATCH_NODE_MOUNTS_DIR` 在节点上访问的标准 `fsmounts` 目录。 确切位置因节点上使用的操作系统而异。 例如，Ubuntu 节点上的物理位置被映射到 `mnt\batch\tasks\fsmounts`，而 CentOS 节点上的物理位置被映射到 `mnt\resources\batch\tasks\fsmounts`。
- **装载选项或 blobfuse 选项**：这些选项描述了用于装载文件系统的特定参数。

创建 `MountConfiguration` 对象后，在创建池时将对象分配给 `MountConfigurationList` 属性。 在节点加入池时，或节点重启或重置映像时，将装载文件系统。

装载文件系统时，将创建环境变量 `AZ_BATCH_NODE_MOUNTS_DIR`，该变量指向装载文件系统以及日志文件的位置，这对于故障排除和调试很有用。 [诊断装载错误](#diagnose-mount-errors)部分中详细说明了日志文件。  

> [!IMPORTANT]
> 池上可装载的文件系统的最大数量为 10。 有关详细信息和其他限制，请参阅 [Batch 服务配额和限制](batch-quota-limit.md#other-limits)。

## <a name="examples"></a>示例

以下代码示例演示如何将各种文件共享装载到计算节点池中。

### <a name="azure-files-share"></a>Azure 文件存储共享

Azure 文件存储是标准的 Azure 云文件系统产品/服务。 若要详细了解如何获取装载配置代码示例中的任何参数，请参阅[使用 Azure 文件存储共享](../storage/files/storage-how-to-use-files-windows.md)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob 文件系统

另一种选择方案是通过 [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) 使用 Azure Blob 存储。 装载 Blob 文件系统需要为存储帐户使用 `AccountKey` 或 `SasKey`。 有关获取这些密钥的信息，请参阅[管理存储帐户访问密钥](../storage/common/storage-account-keys-manage.md)或[使用共享访问签名 (SAS)](../storage/common/storage-sas-overview.md)。 有关使用 blobfuse 的详细信息，请参阅 blobfuse [故障排除常见问题解答](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)。 要获得对 blobfuse 装载目录的默认访问权限，请以管理员身份运行任务。 Blobfuse 在用户空间处将装载目录，在创建池时将其作为根装载。 在 Linux 中，所有管理员任务都是根。 [FUSE 参考页](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)中介绍了 FUSE 模块的所有选择方案。

除了故障排除指南之外，还可借助 blobfuse 存储库中的“GitHub 问题”来检查当前的 blobfuse 问题以及解决方案。 有关详细信息，请参阅 [blobfuse 问题](https://github.com/Azure/azure-storage-fuse/issues)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>网络文件系统

还可以将网络文件系统 (NFS) 装载到池节点，这样可以使 Azure Batch 节点能够轻松访问传统文件系统。 它可以是部署在云中的单个 NFS 服务器，也可以是通过虚拟网络访问的本地 NFS 服务器。 或可以利用 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) 分布式内存中缓存解决方案，该解决方案可提供与本地存储的无缝连接、将数据按需读取到其缓存中，并为基于云的计算节点提供更高的性能和可伸缩性。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>通用 Internet 文件系统

还可以将通用 Internet 文件系统 (CIFS) 装载到池节点，这样可以使 Azure Batch 节点能够轻松访问传统文件系统。 CIFS 是一种文件共享协议，它提供了一种用于请求网络服务器文件和服务的开放和跨平台的机制。 CIFS 基于适用于 Internet 和 Intranet 文件共享的 Microsoft 服务器消息块 (SMB) 协议的增强版本，用于在 Windows 节点上装载外部文件系统。 要详细了解 SMB，请参阅[文件服务器和 SMB](/windows-server/storage/file-server/file-server-smb-overview)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>诊断装载错误

如果装载配置失败，池中的计算节点将失败，节点状态变为不可用。 要诊断某个安装配置失败，请检查 [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) 属性，获取有关错误的详细信息。

要获取日志文件以进行调试，请使用 [OutputFiles](batch-task-output-files.md) 上传 `*.log` 文件。 `*.log` 文件包含有关在 `AZ_BATCH_NODE_MOUNTS_DIR` 位置装载文件系统的信息。 对于每个装载，装载日志文件格式：`<type>-<mountDirOrDrive>.log`。 例如，名为 `test` 的装载目录中处的 `cifs` 装载将有一个名为 `cifs-test.log` 的装载日志文件。

## <a name="supported-skus"></a>支持的 SKU

| 发布者 | 产品/服务 | SKU | Azure 文件存储共享 | Blobfuse | NFS 装载 | CIFS 装载 |
|---|---|---|---|---|---|---|
| 批处理 | rendering-centos73 | 呈现 | :heavy_check_mark: <br>注意：与 CentOS 7.7 兼容</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS、18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>注意：与 CentOS 7.4 兼容。 </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>注意：支持 A_8 或 9 存储</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4、7.3、7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012、2016、2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>后续步骤

- 了解有关将 Azure 存储文件共享装载到 [Windows](../storage/files/storage-how-to-use-files-windows.md) 或 [Linux](../storage/files/storage-how-to-use-files-linux.md) 的详细信息。
- 了解有关使用和装载 [blobfuse](https://github.com/Azure/azure-storage-fuse) 虚拟文件系统的信息。
- 要了解 NFS 及其应用程序，请参阅[网络文件系统概述](/windows-server/storage/nfs/nfs-overview)。
- 要详细了解 SMB，请参阅 [Microsoft SMB 协议和 CIFS 协议概述](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)。
