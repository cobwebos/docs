---
title: "在计算节点上安装应用程序包 - Azure Batch | Microsoft Docs"
description: "使用 Azure Batch 的应用程序包功能轻松管理要安装在 Batch 计算节点上的多个应用程序和版本。"
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: afcc04c80ec15872a22de5d5969a7ef6a583562f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>使用 Batch 应用程序包将应用程序部署到计算节点

使用 Azure Batch 的应用程序包功能可以轻松管理任务应用程序并将其部署到池中的计算节点。 使用应用程序包可以上传和管理任务运行的多个应用程序版本，包括其支持文件。 然后，可以将一个或多个此类应用程序自动部署到池中的计算节点。

本文介绍如何使用 Azure 门户上传和管理应用程序包。 然后，介绍如何使用 [Batch .NET][api_net] 库将包安装到池的计算节点。

> [!NOTE]
> 
> 在 2017 年 7 月 5 日以后创建的所有 Batch 池都支持应用程序包。 在 2016 年 3 月 10 日和 2017 年 7 月 5 日期间创建的 Batch 池也支持应用程序包，但前提是该池是使用云服务配置创建的。 在 2016 年 3 月 10 日以前创建的 Batch 池不支持应用程序包。
>
> 用于创建和管理应用程序包的 API 属于 [Batch Management .NET][[api_net_mgmt]] 库。 用于在计算节点上安装应用程序包的 API 属于 [Batch .NET][api_net] 库。  
>
> 此处所述的应用程序包功能替换了旧版服务中的“批处理应用”功能。
> 
> 

## <a name="application-package-requirements"></a>应用程序包要求
要使用应用程序包，需要[将 Azure 存储帐户链接](#link-a-storage-account)到批处理帐户。

[Batch REST API][api_rest] 2015-12-01.2.2 版和对应的 [Batch .NET][api_net] 库 3.1.0 版引入了此功能。 使用 Batch 时，我们建议始终使用最新的 API 版本。

> [!NOTE]
> 在 2017 年 7 月 5 日以后创建的所有 Batch 池都支持应用程序包。 在 2016 年 3 月 10 日和 2017 年 7 月 5 日期间创建的 Batch 池也支持应用程序包，但前提是该池是使用云服务配置创建的。 在 2016 年 3 月 10 日以前创建的 Batch 池不支持应用程序包。
>
>

## <a name="about-applications-and-application-packages"></a>关于应用程序和应用程序包
在 Azure Batch 中，应用程序是指一组已创建版本的二进制文件，这些文件可自动下载到池中的计算节点。 应用程序包指的是这些二进制文件中的一组特定组合，其代表应用程序的特定版本。

![应用程序和应用程序包的概要关系图][1]

### <a name="applications"></a>应用程序
Batch 中的应用程序包含一个或多个应用程序包，指定应用程序的配置选项。 例如，应用程序可以指定要安装在计算节点上的默认应用程序包版本，以及应用程序的包是否可以更新或删除。

### <a name="application-packages"></a>应用程序包
应用程序包为 .zip 文件，其中包含应用程序二进制文件和应用程序运行任务所需的支持文件。 每个应用程序包都代表特定版本的应用程序。

可以在池和任务级别指定应用程序包。 创建池或任务时，可以指定其中的一个或多个包，以及（可选）指定版本。

* **池应用程序包**部署到池中的每个节点。 当节点加入池以及重新启动或重置映像时，就会部署应用程序。
  
    当池中的所有节点执行作业的任务时，便适合使用池应用程序包。 可以在创建池时指定一个或多个应用程序包，并且可以添加或更新现有池的包。 如果更新现有池的应用程序包，必须重新启动它的节点才能安装新包。
* 在运行任务的命令行之前，**任务应用程序包**只部署到计划要运行任务的计算节点。 如果节点上已有指定的应用程序包和版本，则不会重新部署，而是使用现有包。
  
    在共享池的环境中，任务应用程序包装很有用：不同的操作在一个池上运行，而某项作业完成时并不删除该池。 如果作业中的任务少于池中的节点，任务应用程序包可以减少数据传输，因为应用程序只部署到运行任务的节点。
  
    其他可受益于任务应用程序包的方案为，运行大型应用程序但只执行少数任务的作业。 例如，预处理或合并应用程序非常庞大的预处理阶段或合并任务可能受益于使用任务应用程序包。

> [!IMPORTANT]
> 批处理帐户中的应用程序和应用程序包数目，以及应用程序包的大小上限有其限制。 有关这些限制的详细信息，请参阅 [Azure Batch 服务的配额和限制](batch-quota-limit.md)。
> 
> 

### <a name="benefits-of-application-packages"></a>应用程序包的优点
应用程序包可以简化 Batch 解决方案中的代码，也能降低管理任务运行的应用程序所需的开销。

有了应用程序包，池的启动任务不需要指定在节点上安装一长串的单个资源文件。 不需要在 Azure 存储中或在节点上手动管理应用程序的多个版本。 而且，也不必费心生成 [SAS URL](../storage/common/storage-dotnet-shared-access-signature-part-1.md) 来提供这些文件在存储帐户中的访问权限。 Batch 在后台与 Azure 存储协作来存储应用程序包，并将其部署到计算节点。

> [!NOTE] 
> 启动任务的总大小必须小于或等于 32768 个字符，其中包括资源文件和环境变量。 如果启动任务超出此限制，备选方案就是使用应用程序包。 还可以创建一个包含资源文件的压缩存档，将此存档作为 blob 上传到 Azure 存储，再从启动任务的命令行中解压缩该存档。 
>
>

## <a name="upload-and-manage-applications"></a>上传和管理应用程序
可以使用 [Azure 门户][portal]或 [Batch 管理 .NET](batch-management-dotnet.md) 库来管理批处理帐户中的应用程序包。 在后面几个部分中，将先介绍如何链接存储帐户，再介绍如何使用门户来添加应用程序和包以及管理它们。

### <a name="link-a-storage-account"></a>链接存储帐户
要使用应用程序包，必须先将 Azure 存储帐户链接到 Batch 帐户。 如果尚未配置存储帐户，第一次单击“批处理帐户”边栏选项卡中的“应用程序”磁贴时，Azure 门户会显示警告。

> [!IMPORTANT]
> 批处理目前仅支持“常规用途”存储帐户类型，如[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)的[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)中的步骤 5 所述。 将某个 Azure 存储帐户链接到批处理帐户时，只会链接“常规用途”的存储帐户。
> 
> 

![Azure 门户中显示的“未配置存储帐户”警告][9]

Batch 服务使用关联的存储帐户存储应用程序包。 链接两个帐户后，Batch 便能将存储在链接之存储帐户中的包自动部署到计算节点。 若要将存储帐户链接到批处理帐户，请单击“警告”边栏选项卡中的“存储帐户设置”，再单击“存储帐户”边栏选项卡上的“存储帐户”。

![在 Azure 门户中选择存储帐户边栏选项卡][10]

建议专门创建一个存储帐户用作批处理帐户，并在此处选择该帐户。 有关如何创建存储帐户的详细信息，请参阅[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)中的“创建存储帐户”。 创建存储帐户后，可以使用“存储帐户”边栏选项卡将其链接到 Batch 帐户。

> [!WARNING]
> Batch 服务使用 Azure 存储将应用程序包存储为块 blob。 块 blob 数据按 [正常收费][storage_pricing] 。 请务必考虑应用程序包的大小和数目，并定期删除过时的包以降低成本。
> 
> 

### <a name="view-current-applications"></a>查看当前应用程序
若要查看 Batch 帐户中的应用程序，请在“Batch 帐户”边栏选项卡中，单击左侧菜单中的“应用程序”菜单项。

![应用程序磁贴][2]

选择此菜单选项打开“应用程序”边栏选项卡：

![列出应用程序][3]

“应用程序”边栏选项卡显示帐户中每个应用程序的 ID，以及以下属性：

* **包**：与此应用程序关联的版本号。
* **默认版本**：如果在指定池的应用程序时未指出版本，系统将安装此应用程序版本。 此设置是可选的。
* **允许更新**：该值指定是否允许更新、删除和添加包。 如果此值设置为“否”，将禁用对应用程序包执行更新和删除操作。 只能添加新的应用程序包版本。 默认值为“是”。

### <a name="view-application-details"></a>查看应用程序详细信息
在“应用程序”边栏选项卡中选择应用程序，即可打开包含该应用程序详细信息的边栏选项卡。

![应用程序详细信息][4]

在应用程序详细信息边栏选项卡中，可以配置应用程序的以下设置。

* **允许更新**：指定是否可更新或删除应用程序包。 请参阅下文中的“更新或删除应用程序包”。
* **默认版本**：指定要部署到计算节点的默认应用程序包。
* **显示名称**：指定在显示应用程序相关信息时（例如，通过 Batch 提供给客户的服务 UI 中），Batch 解决方案可以使用的友好名称。

### <a name="add-a-new-application"></a>添加新的应用程序
若要创建新应用程序，请添加应用程序包并指定新的唯一应用程序 ID。 使用新应用程序 ID 添加的第一个应用程序包也会创建新的应用程序。

在“应用程序”边栏选项卡上，单击“添加”，以打开“新建应用程序”边栏选项卡。

![Azure 门户中的新建应用程序边栏选项卡][5]

“新建应用程序”边栏选项卡提供以下字段，用于指定新应用程序和应用程序包的设置。

**应用程序 ID**

此字段指定新应用程序的 ID，需符合标准 Azure Batch ID 验证规则。 提供应用程序 ID 的规则如下所示：

* 在 Windows 节点上，ID 可以包含字母数字字符、连字符和下划线的任意组合。 在 Linux 节点上，仅允许使用字母数字字符和下划线。
* 不能超过 64 个字符。
* 在 Batch 帐户内必须唯一。
* 保留大小写和不区分大小写。

**版本**

此字段指定要上传的应用程序包版本。 版本字符串需符合以下验证规则：

* 在 Windows 节点上，此版本字符串可以包含字母数字字符、连字符、下划线和句点的任意组合。 在 Linux 节点上，此版本字符串只能包含字母数字字符和下划线。
* 不能超过 64 个字符。
* 在应用程序内必须唯一。
* 保留大小写和不区分大小写。

**应用程序包**

指定包含应用程序二进制文件和执行应用程序所需的任何支持文件的 .zip 文件。 单击“选择文件”文本框或文件夹图标，以浏览并选择包含应用程序文件的 .zip 文件。

选择文件后，单击“确定”开始上传到 Azure 存储。 上传操作完成时，门户显示通知并关闭边栏选项卡。 因上传之文件的大小和网络连接速度不尽相同，此操作可能需要花费一些时间。

> [!WARNING]
> 上传操作完成之前，请勿关闭“新建应用程序”边栏选项卡， 否则上传过程将会停止。
> 
> 

### <a name="add-a-new-application-package"></a>添加新应用程序包
若要添加现有应用程序的新应用程序包版本，请在“应用程序”边栏选项卡中选择应用程序，再依序单击“包”和“添加”，以打开“添加包”边栏选项卡。

![Azure 门户中的添加应用程序包边栏选项卡][8]

可以看到，除了“应用程序 ID”文本框已禁用之外，字段与“新建应用程序”边栏选项卡中的字段匹配。 如前面创建新应用程序一样，指定新包的“版本”，浏览到“应用程序包”.zip 文件，并单击“确定”上传包。

### <a name="update-or-delete-an-application-package"></a>更新或删除应用程序包
要更新或删除现有的应用程序包，请打开应用程序的详细信息边栏选项卡，单击“包”以打开“包”边栏选项卡，单击要修改的应用程序包数据列中的**省略号**，并选择要执行的操作。

![在 Azure 门户中更新或删除包][7]

**更新**

单击“更新”时，“更新包”边栏选项卡随即出现。 此边栏选项卡与“新建应用程序包”边栏选项卡相似，只不过包选择字段已启用，因此可以指定要上传的新 ZIP 文件。

![Azure 门户中的更新包边栏选项卡][11]

**删除**

单击“删除”时，系统会要求确认是否要删除包版本，随后 Batch 从 Azure 存储中删除该包。 如果删除应用程序的默认版本，系统会删除应用程序的“默认版本”设置。

![删除应用程序][12]

## <a name="install-applications-on-compute-nodes"></a>将应用程序安装在计算节点上
了解如何使用 Azure 门户管理应用程序包之后，接下来介绍如何使用批处理任务将它们部署到计算节点并运行它们。

### <a name="install-pool-application-packages"></a>安装池应用程序包
要将应用程序包安装在池中的所有计算节点上，需要为池指定一个或多个应用程序包引用。 将每个计算节点加入池以及该节点重新启动或重置映像时，为池指定的应用程序包将安装在该节点上。

在 Batch .NET 中，可以在创建新池时指定一个或多个 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]，或将它们添加到现有池。 [ApplicationPackageReference][net_pkgref] 类指定要安装在池的计算节点上的应用程序 ID 和版本。

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> 如果应用程序包部署出于任何原因而失败，Batch 服务会将该节点标记为 [unusable][net_nodestate]，并且不会在该节点上计划执行任何任务。 在此情况下，应**重启**节点，以重新启动包部署。 重启节点也会在节点上再次启用任务计划。
> 
> 

### <a name="install-task-application-packages"></a>安装任务应用程序包
类似于池，可以为任务指定应用程序包引用。 在节点上计划要运行的任务时，请先下载并解压缩包，然后执行任务的命令行。 如果节点上已安装指定的包和版本，则不会下载包，而是使用现有包。

若要安装任务应用程序包，请配置任务的 [CloudTask][net_cloudtask].[ApplicationPackageReferences][net_cloudtask_pkgref] 属性：

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>执行安装的应用程序
为池或任务指定的包下载并解压缩到节点的 `AZ_BATCH_ROOT_DIR` 中的命名目录。 Batch 还会创建包含命名目录路径的环境变量。 在引用节点上的应用程序时，任务的命令行使用此环境变量。 

在 Windows 节点上，变量格式如下：

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

在 Linux 节点上，该格式略有不同。 句点 (.)、连字符 (-) 和数字符号 (#) 在环境变量中都会转换为下划线。 例如：

```
Linux:
AZ_BATCH_APP_PACKAGE_APPLICATIONID_version
```

`APPLICATIONID` 和 `version` 是对应于为部署指定的应用程序和包版本的值。 例如，如果指定应在 Windows 节点上安装 2.7 版的 blender 应用程序，任务命令行可使用此环境变量来访问其文件：

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

在 Linux 节点上将环境变量指定为以下格式：

```
Linux:
AZ_BATCH_APP_PACKAGE_BLENDER_2_7
``` 

在上传应用程序包时，可以指定要部署到计算节点的默认版本。 如果已指定应用程序的默认版本，可以在引用该应用程序时省略版本后缀。 可以在 Azure 门户的“应用程序”边栏选项卡中指定默认的应用程序版本，如[上载和管理应用程序](#upload-and-manage-applications)中所示。

例如，如果设置“2.7”作为 blender 应用程序的默认版本，并且任务引用以下环境变量，则 Windows 节点将会执行 2.7 版：

`AZ_BATCH_APP_PACKAGE_BLENDER`

以下代码片段显示任务命令行示例，其可启动 blender 应用程序的默认版本：

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> 有关计算节点环境设置的详细信息，请参阅 [Batch 功能概述](batch-api-basics.md)中的[任务的环境设置](batch-api-basics.md#environment-settings-for-tasks)。
> 
> 

## <a name="update-a-pools-application-packages"></a>更新池的应用程序包
如果已配置现有池的应用程序包，可以指定池的新包。 如果为池指定新的包引用，请遵循以下规则：

* Batch 服务在联接池的所有新节点上以及任何重新启动或重置映像的现有节点上都会安装新指定的包。
* 在更新包引用时已存在池中的计算节点不自动安装新应用程序包。 这些计算节点必须重新启动或重置映像才能接收新包。
* 部署新包后，创建的环境变量将反映新的应用程序包引用。

在此示例中，现有池已将应用程序 blender 的 2.7 版设置为其中一个 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]。 要将池的节点更新为 2.76b 版，请将 [ApplicationPackageReference][net_pkgref] 指定为新版本并提交更改。

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

配置新版本后，Batch 服务将在任何联接池的新节点上安装 2.76b 版。 要将 2.76b 安装到已在池中的节点上，请重新启动或重置映像这些节点。 请注意，重新启动的节点会保留前面的包部署中的文件。

## <a name="list-the-applications-in-a-batch-account"></a>列出 Batch 帐户中的应用程序
可以使用 [ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries] 方法列出批处理帐户中的应用程序和应用程序包。

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>总结
当客户使用提供的启用 Batch 服务来处理操作时，可以通过应用程序包帮助他们选择作业的应用程序，以及指定要使用的确切版本。 你还可以在服务中提供让客户上传及跟踪其应用程序的功能。

## <a name="next-steps"></a>后续步骤
* [Batch REST API][api_rest] 还提供应用程序包的使用支持。 有关示例，请参阅[将池添加到帐户][rest_add_pool]中的 [applicationPackageReferences][rest_add_pool_with_packages] 元素，了解如何使用 REST API 指定要安装的包。 若要深入了解如何使用 Batch REST API 获取应用程序信息，请参阅[应用程序][rest_applications]。
* 了解如何以编程方式[使用 Batch Management .NET 管理 Azure Batch 帐户和配额](batch-management-dotnet.md)。 [Batch Management .NET][api_net_mgmt] 库可以启用 Batch 应用程序或服务的帐户创建和删除功能。

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/en-us/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "应用程序包概要关系图"
[2]: ./media/batch-application-packages/app_pkg_02.png "Azure 门户中的应用程序磁贴"
[3]: ./media/batch-application-packages/app_pkg_03.png "Azure 门户中的应用程序边栏选项卡"
[4]: ./media/batch-application-packages/app_pkg_04.png "Azure 门户中的应用程序详细信息边栏选项卡"
[5]: ./media/batch-application-packages/app_pkg_05.png "Azure 门户中的新建应用程序边栏选项卡"
[7]: ./media/batch-application-packages/app_pkg_07.png "Azure 门户中的更新或删除包下拉列表"
[8]: ./media/batch-application-packages/app_pkg_08.png "Azure 门户中的新建应用程序包边栏选项卡"
[9]: ./media/batch-application-packages/app_pkg_09.png "未链接存储帐户警报"
[10]: ./media/batch-application-packages/app_pkg_10.png "在 Azure 门户中选择存储帐户边栏选项卡"
[11]: ./media/batch-application-packages/app_pkg_11.png "Azure 门户中的更新包边栏选项卡"
[12]: ./media/batch-application-packages/app_pkg_12.png "Azure 门户中的删除包确认对话框"
