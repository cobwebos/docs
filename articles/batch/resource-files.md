---
title: 创建和使用资源文件-Azure Batch
description: 了解如何从各种输入源创建批处理资源文件。 本文介绍了有关如何创建虚拟机并将其放置到 VM 上的一些常用方法。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: e890bce378327fe5b1f4068d6719e6b905404f3c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390055"
---
# <a name="creating-and-using-resource-files"></a>创建和使用资源文件

Azure Batch 任务通常需要某种形式的数据才能处理。 资源文件是通过任务将此数据提供给批处理虚拟机（VM）的一种方式。 所有类型的任务都支持资源文件：任务、启动任务、作业准备任务、作业释放任务等。本文介绍了有关如何创建资源文件并将其放置到 VM 上的一些常用方法。  

资源文件是一种机制，用于将数据成批地放入 VM，但数据的类型和使用方式是灵活的。 但有一些常见的用例：

1. 使用启动任务上的资源文件在每个 VM 上预配公共文件
1. 设置任务要处理的输入数据

例如，在用于安装任务运行的应用程序的启动任务上，可能会使用常见文件。 输入数据可以是原始图像或视频数据，也可以是批处理要处理的任何信息。

## <a name="types-of-resource-files"></a>资源文件的类型

有几个不同的选项可用于生成资源文件。 资源文件的创建进程因原始数据的存储位置而异。

用于创建资源文件的选项：

- [存储容器 URL](#storage-container-url)：从 Azure 中的任何存储容器生成资源文件
- [存储容器名称](#storage-container-name)：从链接到 Batch 的 Azure 存储帐户中的容器名称生成资源文件
- [Web 终结点](#web-endpoint)：从任何有效 HTTP URL 生成资源文件

### <a name="storage-container-url"></a>存储容器 URL

使用存储容器 URL 意味着可以使用正确的权限访问 Azure 中任何存储容器中的文件。

在此C#示例中，文件已上传到 Azure 存储容器作为 blob 存储。 若要访问创建资源文件所需的数据，首先需要获取对存储容器的访问权限。

创建具有正确权限的共享访问签名（SAS） URI 以访问存储容器。 为 SAS 设置过期时间和权限。 在这种情况下，不指定开始时间，因此 SAS 会立即生效并在生成后两小时后过期。

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 对于容器访问，必须同时拥有 `Read` 和 `List` 权限，而使用 blob 访问权限时，只需 `Read` 权限。

配置权限后，创建 SAS 令牌并格式化 SAS URL 以访问存储容器。 使用存储容器的格式化 SAS URL，生成具有[`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)的资源文件。

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

生成 SAS URL 的替代方法是启用对 Azure Blob 存储中的容器及其 blob 的匿名公共读取访问。 通过这样做，你可以授予对这些资源的只读访问权限，而无需共享帐户密钥，也无需使用 SAS。 公共读取访问通常用于希望某些 blob 始终可用于匿名读取访问的情况。 如果此方案适用于你的解决方案，请参阅对[blob 的匿名访问](../storage/blobs/storage-manage-access-to-resources.md)一文，详细了解如何管理对 blob 数据的访问。

### <a name="storage-container-name"></a>存储容器名称

可以使用 Azure 存储容器的名称访问 blob 数据，而不是配置和创建 SAS URL。 所使用的存储容器需要位于链接到 Batch 帐户的 Azure 存储帐户中，该帐户称为 autostorage-keys 帐户。 使用 autostorage-keys 帐户的存储容器名称，可以绕过配置和创建用于访问存储容器的 SAS URL。

在此示例中，我们假设要用于创建资源文件的数据已位于链接到 Batch 帐户的 Azure 存储帐户中。 如果没有 autostorage-keys 帐户，请参阅[创建批处理帐户](batch-account-create-portal.md)中的步骤，了解有关如何创建和链接帐户的详细信息。

通过使用链接的存储帐户，无需创建和配置存储容器的 SAS URL。 相反，请在链接的存储帐户中提供存储容器的名称。

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Web 终结点

尚未上传到 Azure 存储的数据仍可用于创建资源文件。 可以指定包含输入数据的任何有效 HTTP URL。 URL 将提供给批处理 API，然后使用数据创建资源文件。

在下面C#的示例中，输入数据托管在虚拟 GitHub 终结点上。 API 从有效的 web 终结点检索文件，并生成任务要使用的资源文件。 此方案不需要任何凭据。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>提示和建议

每个 Azure Batch 任务都以不同的方式使用文件，这就是批处理包含可用于管理任务文件的选项的原因。 以下方案并不全面，只是介绍了几种常见情况，并提供了建议。

### <a name="many-resource-files"></a>许多资源文件

你的批处理作业可能包含多个任务，这些任务使用相同的公共文件。 如果常见任务文件在许多任务间共享，使用应用程序包来包含文件而不是使用资源文件可能是更好的选择。 应用程序包提供下载速度优化。 此外，应用程序包中的数据缓存在任务之间，因此，如果任务文件不经常更改，则应用程序包可能适用于你的解决方案。 对于应用程序包，无需手动管理多个资源文件或生成 SAS Url 即可访问 Azure 存储中的文件。 Batch 在后台与 Azure 存储配合使用，可将应用程序包存储和部署到计算节点。

如果每个任务都具有该任务独有的多个文件，则资源文件很可能是最佳选择。 通常需要更新或替换使用唯一文件的任务，这与应用程序包内容无关。 资源文件为更新、添加或编辑单个文件提供了额外的灵活性。

### <a name="number-of-resource-files-per-task"></a>每个任务的资源文件数

如果在任务上指定了几百个资源文件，则批处理可能会拒绝该任务太大。 最好将任务本身的资源文件数量减到最小，从而使任务更小。

如果无法最大程度地减少任务所需的文件数，可以通过创建引用资源文件存储容器的单个资源文件来优化任务。 为此，请将资源文件放入 Azure 存储容器，并使用不同的资源文件 "容器" 模式。 使用 blob 前缀选项来指定要为你的任务下载的文件集合。

## <a name="next-steps"></a>后续步骤

- 了解[应用程序包](batch-application-packages.md)作为资源文件的替代方法。
- 有关为资源文件使用容器的详细信息，请参阅[容器工作负荷](batch-docker-container-workloads.md)。
- 若要了解如何收集和保存任务的输出数据，请参阅[持久保存作业和任务输出](batch-task-output.md)。
- 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。
