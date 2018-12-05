---
title: Azure Batch 上的容器工作负载 | Microsoft Docs
description: 了解如何在 Azure Batch 上从容器映像中运行应用程序。
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 11/19/2018
ms.author: danlep
ms.openlocfilehash: 1d915482a3a8b1f6416b50ab52de997a9d33294f
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262425"
---
# <a name="run-container-applications-on-azure-batch"></a>在 Azure Batch 上运行容器应用程序

可以通过 Azure Batch 在 Azure 上运行和缩放大量批处理计算作业。 Batch 任务可直接在 Batch 池中的虚拟机（节点）上运行；但也可以设置一个 Batch 池，以便在节点上的 Docker 兼容容器中运行任务。 本文介绍如何创建支持运行容器任务的计算节点池，然后在池中运行容器任务。 

读者应熟悉容器的概念，并知道如何创建 Batch 池和作业。 以下代码示例使用 Batch .NET 和 Python SDK。 也可以使用其他 Batch SDK 和工具，包括 Azure 门户，来创建支持容器的 Batch 池，以及运行容器任务。

## <a name="why-use-containers"></a>为何使用容器？

使用容器可以方便地运行 Batch 任务，无需管理环境和依赖项即可运行应用程序。 容器将应用程序部署为轻量级、可移植、自给自足的单元，可以在各种不同的环境中运行。 例如，在本地构建和测试容器，然后将容器映像上传到 Azure 或其他位置的注册表中。 容器部署模型可确保始终正确安装和配置应用程序的运行时环境，而不考虑在何处托管应用程序。 Batch 中基于容器的任务也可利用非容器任务的功能，包括应用程序包以及资源文件和输出文件的管理。 

## <a name="prerequisites"></a>先决条件

* **SDK 版本**：Batch SDK 支持到以下版本为止的容器映像：
    * Batch REST API 版本 2017-09-01.6.0
    * Batch .NET SDK 版本 8.0.0
    * Batch Python SDK 版本 4.0
    * Batch Java SDK 版本 3.0
    * Batch Node.js SDK 版本 3.0

* **帐户**：在 Azure 订阅中，需要创建 Batch 帐户和 Azure 存储帐户（后者为可选）。

* **支持的 VM 映像**：容器仅在使用来自映像的虚拟机配置创建的池中受支持。有关详细信息，请参阅下一节：“受支持的虚拟机映像”。 如果提供自定义映像，请参阅以下部分所述的注意事项，以及[使用托管的自定义映像创建虚拟机池](batch-custom-images.md)中所述的要求。 

### <a name="limitations"></a>限制

* 批处理仅对在 Linux 池上运行的容器提供 RDMA 支持。

* 对于 Windows 容器工作负荷，建议为你的池选择多核 VM 大小

## <a name="supported-virtual-machine-images"></a>受支持的虚拟机映像

使用以下受支持的 Windows 或 Linux 映像之一，为容器工作负荷创建 VM 计算节点池。 有关与 Batch 兼容的市场映像的详细信息，请参阅[虚拟机映像列表](batch-linux-nodes.md#list-of-virtual-machine-images)。 

### <a name="windows-images"></a>Windows 映像

对于 Windows 容器工作负荷，Batch 目前支持 Azure 市场中的**包含容器的 Windows Server 2016 Datacenter**。 Windows 上仅支持 Docker 容器映像。

也可以从 Windows 上运行 Docker 的 VM 创建自定义映像。

### <a name="linux-images"></a>Linux 映像

对于 Linux 容器工作负荷，Batch 目前支持 Microsoft Azure Batch 在 Azure 市场中发布的以下 Linux 映像：

* **适用于 Azure Batch 容器池的 CentOS**

* **适用于 Azure Batch 容器池的 CentOS（包含 RDMA 驱动程序）**

* **适用于 Azure Batch 容器池的 Ubuntu 服务器**

* **适用于 Azure Batch 容器池的 Ubuntu 服务器（包含 RDMA 驱动程序）**

仅支持在 Azure Batch 池中使用这些映像。 这些映像具有以下特性：

* 预装了 [Moby](https://github.com/moby/moby) 容器运行时 

* 预装了 NVIDIA GPU 驱动程序，以简化 Azure N 系列 VM 上的部署

* 预装或未预装 RDMA 驱动程序的选定映像。 在支持 RDMA 的 VM 上部署池节点后，这些驱动程序可让池节点访问 Azure RDMA 网络。 

也可以从与 Batch 兼容的 Linux 分发版之一上运行 Docker 的 VM 创建自定义映像。 如果选择提供你自己的自定义 Linux 映像，请参阅[使用托管自定义映像创建虚拟机池](batch-custom-images.md)中的说明。

若要在自定义映像中获取 Docker 支持，请安装 [Docker Community Edition (CE)](https://www.docker.com/community-edition) 或 [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition)。

使用自定义 Linux 映像时的其他注意事项：

* 若要在使用自定义映像时利用 Azure N 系列大小的 GPU 性能，请预装 NVIDIA 驱动程序。 此外，还需要安装 NVIDIA GPU 的 Docker 引擎实用工具 [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker)。

* 若要访问 Azure RDMA 网络，请使用支持 RDMA 的 VM 大小。 Batch 支持的 CentOS HPC 和 Ubuntu 映像中已安装所需的 RDMA 驱动程序。 可能还需要其他配置来运行 MPI 工作负载。 请参阅[在批处理池中使用支持 RDMA 或启用了 GPU 的实例](batch-pool-compute-intensive-sizes.md)。


## <a name="container-configuration-for-batch-pool"></a>批处理池的容器配置

若要启用运行容器工作负荷的 Batch 池，必须在池的 [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) 对象中指定 [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) 设置。 （本文已提供 Batch .NET API 参考文章的链接。 [Batch Python](/python/api/azure.batch) API 中提供了相应的设置。）

可以创建启用容器的池，可以带或不带预提取容器映像，如以下示例所示。 可以通过拉取（或预提取）过程从 Docker 中心预加载容器映像，或在 Internet 上预加载另一个容器注册表。 为获得最佳性能，请使用 Batch 帐户所在的同一区域中的 [Azure 容器注册表](../container-registry/container-registry-intro.md)。

预提取容器映像的优点是，当任务开始运行时，它们不必等待要下载的容器映像。 容器配置在创建池时将容器映像拉取到 VM。 然后，在池中运行的任务即可引用容器映像和容器运行选项列表。


### <a name="pool-without-prefetched-container-images"></a>不带预提取容器映像的池

若要配置启用了容器但不带预提取容器映像的池，请定义 `ContainerConfiguration` 和 `VirtualMachineConfiguration` 对象，如以下 Python 示例所示。 此示例使用市场中提供的适用于 Azure Batch 容器池映像的 Ubuntu 服务器。


```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            container_configuration=container_conf,
            node_agent_sku_id='batch.node.ubuntu 16.04'),
        vm_size='STANDARD_D1_V2',
        target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>容器配置的预提取映像

若要在池中预提取容器映像，请将容器映像列表（在 Python 中为 `container_image_names`）添加到 `ContainerConfiguration`。 

以下基本 Python 示例演示如何从 [Docker 中心](https://hub.docker.com)预提取一个标准的 Ubuntu 容器映像。

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


以下 C# 示例假设要从 [Docker 中心](https://hub.docker.com)预提取一个 TensorFlow 映像。 本示例包含一个在池节点的 VM 主机中运行的启动任务。 可以在主机中运行具有特定目的（例如，装载可以从容器访问的文件服务器）的启动任务。

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>从专用容器注册表中预提取映像

另外，也可以通过对专有容器注册表服务器进行身份验证来预提取容器映像。 在以下示例中，`ContainerConfiguration` 和 `VirtualMachineConfiguration` 对象从专用的 Azure 容器注册表预提取专用的 TensorFlow 映像。 图像中的参考信息与前面的示例相同。

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>任务容器设置

若要在启用了容器的池上运行容器任务，请指定特定于容器的设置。 设置包括要使用的映像、注册表和容器运行选项。

* 使用任务类中的 `ContainerSettings` 属性来配置特定于容器的设置。 这些设置由 [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) 类定义。

* 如果在容器映像上运行任务，[云任务](/dotnet/api/microsoft.azure.batch.cloudtask)和[作业管理器任务](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask)将需要容器设置。 但是，[启动任务](/dotnet/api/microsoft.azure.batch.starttask)、[作业准备任务](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)和[作业发布任务](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)都不需要容器设置（即，它们可以在容器上下文中或直接在节点上运行）。

### <a name="container-task-command-line"></a>容器任务命令行

运行容器任务时，Batch 自动使用 [docker create](https://docs.docker.com/engine/reference/commandline/create/) 命令通过任务中指定的映像创建容器。 之后，Batch 会控制容器中的任务执行。 

与非容器 Batch 任务一样，为容器任务设置命令行。 由于 Batch 自动创建容器，因此命令行仅指定将在容器中运行的命令。

如果 Batch 任务的容器映像配置了 [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) 脚本，则可以将命令行设置为使用默认的 ENTRYPOINT 或覆盖它： 

* 若要使用容器映像的默认 ENTRYPOINT，请将任务命令行设置为空字符串 `""`。

* 若要覆盖默认的 ENTRYPOINT，或者如果映像没有 ENTRYPOINT，请设置适用于容器的命令行，例如 `/app/myapp` 或 `/bin/sh -c python myscript.py`。

可选的 [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) 是为 Batch 用于创建和运行容器的 `docker create` 命令提供的附加参数。 例如，若要为容器设置工作目录，请设置 `--workdir <directory>` 选项。 有关其他选项，请参阅 [docker create](https://docs.docker.com/engine/reference/commandline/create/) 参考。

### <a name="container-task-working-directory"></a>容器任务工作目录

Batch 容器任务在容器的工作目录中执行，该目录与 Batch 为常规（非容器）任务设置的目录非常相似。 请注意，此工作目录与映像中的 [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir)（如果配置）或默认容器工作目录（Windows容器上的 `C:\` 或 Linux 容器上的 `/`）不同。 

对于 Batch 容器任务：

* 以递归方式位于主机节点上的 `AZ_BATCH_NODE_ROOT_DIR`（Azure Batch 目录的根）下的所有目录都映射到容器中
* 所有任务环境变量都映射到该容器
* 节点上的任务工作目录 `AZ_BATCH_TASK_WORKING_DIR` 设置为与常规任务相同并映射到容器中。 

这些映射允许以与处理非容器任务大致相同的方式处理容器任务。 例如，使用应用程序包安装应用程序，从 Azure 存储访问资源文件，使用任务环境设置，以及在容器停止后保留任务输出文件。

### <a name="troubleshoot-container-tasks"></a>对容器任务进行故障排除

如果容器任务未按预期运行，则可能需要获取有关容器映像的 WORKDIR 或 ENTRYPOINT 配置的信息。 若要查看配置，请运行 [docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/) 命令。 

如果需要，请根据映像调整容器任务的设置：

* 在任务命令行中指定绝对路径。 如果映像的默认 ENTRYPOINT 用于任务命令行，请确保设置了绝对路径。

* 在任务的容器运行选项中，更改工作目录以匹配映像中的 WORKDIR。 例如，设置 `--workdir /app`。

## <a name="container-task-examples"></a>容器任务示例

以下 Python 代码片段展示了根据从 Docker 中心拉取的虚构映像创建的容器中运行的基本命令行。 此处，`--rm` 容器选项用于在任务完成后删除容器，`--workdir` 选项用于设置工作目录。 该命令行使用一个简单的 shell 命令（可将小文件写入主机上的任务工作目录）覆盖容器 ENTRYPOINT。 

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage', 
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)

```

以下 C# 示例演示某个云任务的基本容器设置：

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>后续步骤

* 另请参阅 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 工具包，以便通过 [Shipyard 窍门](https://github.com/Azure/batch-shipyard/tree/master/recipes)在 Azure Batch 上轻松部署容器工作负荷。

* 有关在 Linux 上安装和使用 Docker CE 的详细信息，请参阅 [Docker](https://docs.docker.com/engine/installation/) 文档。

* 有关使用自定义映像的详细信息，请参阅[使用托管的自定义映像创建虚拟机池](batch-custom-images.md)。

* 详细了解 [Moby 项目](https://mobyproject.org/)（用于创建基于容器的系统的框架）。