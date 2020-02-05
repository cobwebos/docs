---
title: 在池 Azure Batch 上装载虚拟文件系统 |Microsoft Docs
description: 了解如何在批处理池上装载虚拟文件系统。
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: a22117505dff35f9b92e3dd3c91dc8540557b218
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023032"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>在 Batch 池中装载虚拟文件系统

Azure Batch 现在支持在批处理池中的 Windows 或 Linux 计算节点上装载云存储或外部文件系统。 计算节点加入池时，会装载虚拟文件系统并将其视为该节点上的本地驱动器。 可以装载文件系统，例如 Azure 文件、Azure Blob 存储、网络文件系统（NFS），包括[Avere vFXT 缓存](../avere-vfxt/avere-vfxt-overview.md)或常见 Internet 文件系统（CIFS）。

本文介绍如何使用[适用于 .net 的批处理管理库](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet)在计算节点池上装载虚拟文件系统。

> [!NOTE]
> 在2019-08-19 或之后创建的批处理池支持装载虚拟文件系统。 在2019-08-19 之前创建的批处理池不支持此功能。
> 
> 用于装载计算节点上的文件系统的 Api 属于[Batch .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)库。

## <a name="benefits-of-mounting-on-a-pool"></a>在池上装载的优点

将文件系统安装到池中，而不是让任务从大型数据集中检索自己的数据，从而使任务更轻松、更高效地访问所需的数据。

假设有多个任务需要访问一组公共数据（如渲染电影）的方案。 每个任务每次从场景文件呈现一个或多个帧。 通过挂载包含场景文件的驱动器，计算节点访问共享数据会更容易。 此外，可以根据同时访问数据的计算节点数所需的性能和规模（吞吐量和 IOPS），单独选择和缩放基础文件系统。 例如，可以使用[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)分布式内存中缓存来支持大的动画规模呈现，其中包含数千个并发呈现节点，并访问驻留在本地的源数据。 或者，对于已驻留在基于云的 Blob 存储中的数据，可以使用[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)将此数据作为本地文件系统进行装载。 Blobfuse 仅可在 Linux 节点上使用，但[Azure 文件](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/)提供了类似的工作流，并可在 Windows 和 Linux 上使用。

## <a name="mount-a-virtual-file-system-on-a-pool"></a>在池上装载虚拟文件系统  

在池上装载虚拟文件系统会使文件系统可用于池中的每个计算节点。 当计算节点加入池时，或在节点重新启动或重置映像时，将配置文件系统。

若要在池上装载文件系统，请创建 `MountConfiguration` 对象。 选择适合虚拟文件系统的对象： `AzureBlobFileSystemConfiguration`、`AzureFileShareConfiguration`、`NfsMountConfiguration`或 `CifsMountConfiguration`。

所有装载配置对象都需要以下基本参数。 某些装载配置具有特定于所使用的文件系统的参数，这些参数将在代码示例中更详细地讨论。

- **帐户名或源**：若要装载虚拟文件共享，需要存储帐户或其源的名称。
- **相对装载路径或源**：在计算节点上装载的文件系统的位置，相对于通过 `AZ_BATCH_NODE_MOUNTS_DIR`在节点上可访问的标准 `fsmounts` 目录。 确切位置根据节点上使用的操作系统的不同而不同。 例如，Ubuntu 节点上的物理位置将映射到 `mnt\batch\tasks\fsmounts`，并在 CentOS 节点上映射到 `mnt\resources\batch\tasks\fsmounts`。
- **装载选项或 blobfuse 选项**：这些选项描述用于装载文件系统的特定参数。

创建 `MountConfiguration` 对象之后，在创建池时将对象分配给 `MountConfigurationList` 属性。 当节点加入池或节点重新启动或重置映像时，将装载文件系统。

装载文件系统时，会创建一个环境变量 `AZ_BATCH_NODE_MOUNTS_DIR`，该变量指向已装载的文件系统的位置以及日志文件，这对于疑难解答和调试非常有用。 [诊断装载错误](#diagnose-mount-errors)一节中更详细地介绍了日志文件。  

> [!IMPORTANT]
> 池上装载的文件系统的最大数目为10。 有关详细信息和其他限制，请参阅[Batch 服务配额和限制](batch-quota-limit.md#other-limits)。

## <a name="examples"></a>示例

下面的代码示例演示如何将各种文件共享装载到计算节点池。

### <a name="azure-files-share"></a>Azure 文件共享

Azure 文件是标准的 Azure 云文件系统产品。 若要详细了解如何获取装入配置代码示例中的任何参数，请参阅[使用 Azure 文件共享](../storage/files/storage-how-to-use-files-windows.md)。

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
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob 文件系统

另一种方法是通过[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)使用 Azure Blob 存储。 装载 blob 文件系统需要为存储帐户 `AccountKey` 或 `SasKey`。 有关获取这些密钥的信息，请参阅[管理存储帐户访问密钥](../storage/common/storage-account-keys-manage.md)或[使用共享访问签名（SAS）](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 有关使用 blobfuse 的详细信息，请参阅 blobfuse[问题解答常见问题解答](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)。 若要获取对 blobfuse 装载目录的默认访问权限，请以**管理员身份**运行该任务。 Blobfuse 会在用户空间装载目录，并在创建池时将其作为根挂载。 在 Linux 中，所有**管理员**任务都是 root。 熔断器[参考页](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)中介绍了保险丝模块的所有选项。

除了故障排除指南之外，blobfuse 存储库中的 GitHub 问题也是查看当前 blobfuse 问题和解决方法的有用方法。 有关详细信息，请参阅[blobfuse 问题](https://github.com/Azure/azure-storage-fuse/issues)。

> [!NOTE]
> Blobfuse 目前不支持 Debian。 有关详细信息，请参阅[支持的 sku](#supported-skus) 。

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

网络文件系统（NFS）还可以装载到池节点，这使得 Azure Batch 节点可以轻松地访问传统文件系统。 这可能是部署在云中的单个 NFS 服务器或通过虚拟网络访问的本地 NFS 服务器。 另外，还可以利用[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)分布式内存中缓存解决方案，该解决方案提供与本地存储的无缝连接，将数据按需读取到其缓存中，并提供高性能和扩展到基于云的计算节点。

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

### <a name="common-internet-file-system"></a>常见 Internet 文件系统

通用 Internet 文件系统（CIFS）还可以装载到池节点，从而使 Azure Batch 的节点可以轻松地访问传统文件系统。 CIFS 是一种文件共享协议，提供用于请求网络服务器文件和服务的开放和跨平台机制。 CIFS 基于适用于 internet 和 intranet 文件共享的 Microsoft 服务器消息块（SMB）协议的增强版，并用于在 Windows 节点上装载外部文件系统。 若要了解有关 SMB 的详细信息，请参阅[文件服务器和 SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview)。

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

如果装载配置失败，则池中的计算节点将失败，并且节点状态变为不可用。 若要诊断装入配置失败，请检查[`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)属性以获取有关错误的详细信息。

若要获取日志文件以进行调试，请使用[OutputFiles](batch-task-output-files.md)上传 `*.log` 文件。 `*.log` 文件包含 `AZ_BATCH_NODE_MOUNTS_DIR` 位置的文件系统装载的相关信息。 对于每个装载，装载日志文件的格式为： `<type>-<mountDirOrDrive>.log`。 例如，在名为 `test` 的安装目录中装入 `cifs` 将有一个名为 "`cifs-test.log`" 的装载日志文件。

## <a name="supported-skus"></a>支持的 SKU

| 发布者 | 产品 | SKU | Azure 文件共享 | Blobfuse | NFS 装载 | CIFS 装载 |
|---|---|---|---|---|---|---|
| batch | rendering-centos73 | 呈现 | :heavy_check_mark: <br>注意：与 CentOS 7.7 兼容</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS、18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8、9 | :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>注意：与 CentOS 7.4 兼容。 </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>注意：支持 A_8 或9个存储</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4、7.3、7。1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012、2016、2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>后续步骤

- 详细了解如何在[Windows](../storage/files/storage-how-to-use-files-windows.md)或[Linux](../storage/files/storage-how-to-use-files-linux.md)中装载 Azure 文件共享。
- 了解如何使用和装载[blobfuse](https://github.com/Azure/azure-storage-fuse)虚拟文件系统。
- 请参阅[网络文件系统概述](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)，了解 NFS 及其应用程序。
- 有关 CIFS 的详细信息，请参阅[MICROSOFT SMB 协议和 CIFS 协议概述](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)。
