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
ms.date: 05/07/2018
ms.author: danlep
ms.openlocfilehash: 8c9f772c9d3908e450961239797f6ce2bd4982e4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885870"
---
# <a name="run-container-applications-on-azure-batch"></a>在 Azure Batch 上运行容器应用程序

可以通过 Azure Batch 在 Azure 上运行和缩放大量批处理计算作业。 到目前为止，批处理任务已直接在批处理池中的虚拟机 (VM) 上运行，但现在可以设置一个批处理池在 Docker 容器中运行任务。 本文介绍了如何使用 Batch .NET SDK 创建支持运行容器任务的计算节点池，以及如何在池上运行容器任务。

使用容器可以方便地运行 Batch 任务，无需管理环境和依赖项即可运行应用程序。 容器将应用程序部署为轻量级、可移植、自给自足的单元，可以在各种不同环境中运行。 例如，你可以在本地构建和测试容器，然后将容器映像上传到 Azure 或其他位置的注册表中。 容器部署模型可确保始终正确安装和配置应用程序的运行时环境，而不考虑在何处托管应用程序。 Batch 中基于容器的任务也可利用非容器任务的功能，包括应用程序包以及资源文件和输出文件的管理。 

本文假定你熟悉 Docker 容器概念，并了解如何使用 .NET SDK 创建批处理池和作业。 代码片段主要用在客户端应用程序中，类似于 [DotNetTutorial 示例](batch-dotnet-get-started.md)，并且是批处理中支持容器应用程序所需的代码示例。


## <a name="prerequisites"></a>先决条件

* **SDK 版本**：Batch SDK 支持到以下版本为止的容器映像：
    * Batch REST API 版本 2017-09-01.6.0
    * Batch .NET SDK 版本 8.0.0
    * Batch Python SDK 版本 4.0
    * Batch Java SDK 版本 3.0
    * Batch Node.js SDK 版本 3.0

* **帐户**：在 Azure 订阅中，需要创建 Batch 帐户和 Azure 存储帐户（后者为可选）。

* **支持的 VM 映像**：容器仅在使用来自映像的虚拟机配置创建的池中受支持。有关详细信息，请参阅下一节：“受支持的虚拟机映像”。 如果你提供自定义映像，则应用程序必须使用 Azure Active Directory [(Azure AD) 身份验证](batch-aad-auth.md)才能运行基于容器的工作负荷。 


## <a name="supported-virtual-machine-images"></a>受支持的虚拟机映像

需要使用受支持的 Windows 或 Linux 映像才能为容器工作负荷创建 VM 计算节点池。

### <a name="windows-images"></a>Windows 映像

对于 Windows 容器工作负载，批处理目前支持从在 Windows 上运行 Docker 的 VM 中创建的自定义映像，或者可以将 Windows Server 2016 Datacenter 与 Azure Marketplace 中的容器映像结合使用。 此映像将与 `batch.node.windows amd64` 节点代理 SKU ID 兼容。 受支持的容器类型当前限制为 Docker。

### <a name="linux-images"></a>Linux 映像

对于 Linux 容器工作负载，批处理目前仅支持在以下 Linux 分发版上运行 Docker 的 VM 中创建的自定义映像：Ubuntu 16.04 LTS 或 CentOS 7.3。 如果选择提供你自己的自定义 Linux 映像，请参阅[使用托管自定义映像创建虚拟机池](batch-custom-images.md)中的说明。

若要获取 Docker 支持，请安装 [Docker 社区版 (CE)](https://www.docker.com/community-edition) 或 [Docker 企业版 (EE)](https://www.docker.com/enterprise-edition)。

如果想要利用 Azure NC 或 NV VM 大小的 GPU 性能，则需要在映像上安装 NVIDIA 驱动程序。 此外，还需要安装和运行 NVIDIA GPU 的 Docker 引擎实用工具，[NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker)。

若要访问 Azure RDMA 网络，请使用支持 RDMA 的 VM 大小，例如 A8、A9、H16r、H16mr 或 NC24r。 必需的 RDMA 驱动程序安装在 Azure Marketplace 中的 CentOS 7.3 HPC 和 Ubuntu 16.04 LTS 映像中。 可能还需要其他配置来运行 MPI 工作负载。 请参阅[在批处理池中使用支持 RDMA 或启用了 GPU 的实例](batch-pool-compute-intensive-sizes.md)。


## <a name="limitations"></a>限制

* 批处理仅对在 Linux 池上运行的容器提供 RDMA 支持。


## <a name="authenticate-using-azure-active-directory"></a>使用 Azure Active Directory 进行身份验证

如果使用自定义 VM 映像创建批处理池，则客户端应用程序必须使用 Azure AD 集成身份验证进行身份验证（共享密钥身份验证不起作用）。 在运行应用程序之前，请确保在 Azure AD 中注册它以为其建立标识，并指定它对其他应用程序的权限。

此外，当你使用自定义 VM 映像时，需要向应用程序授予 IAM 访问控制，以访问 VM 映像。 在 Azure 门户中单击“所有资源”，选择容器映像，然后在映像页的“访问控制 (IAM)”部分单击“添加”。 在“添加权限”页中指定“角色”，在“分配访问权限”中选择“Azure AD 用户、组或应用程序”，然后在“选择”中输入应用程序名称。

在应用程序中，请在创建 Batch 客户端时传递 Azure AD 身份验证令牌。 如果使用 Batch .NET SDK 进行开发，请使用 [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_)，如[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)中所述。


## <a name="reference-a-vm-image-for-pool-creation"></a>引用用于池创建的 VM 映像

在应用程序代码中，提供对 VM 映像的引用，用于创建池的计算节点。 通过创建 [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) 对象来执行此操作。 可以通过以下方式之一指定要使用的映像：

* 如果你正在使用自定义映像，请为虚拟机映像提供 Azure 资源管理器资源标识符。 映像标识符具有路径格式，如以下示例所示：

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    若要从 Azure 门户获取此映像 ID，请打开“所有资源”，选择自定义映像，并从映像页的“概述”部分复制“资源 ID”中的路径。

* 如果使用的是 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based) 映像，则提供一组描述映像的参数：发布者、产品/服务类型、SKU 和映像版本，如[虚拟机映像列表](batch-linux-nodes.md#list-of-virtual-machine-images)中所示：

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>批处理池的容器配置

若要启用运行容器工作负荷的 Batch 池，必须在池的 [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) 对象中指定 [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) 设置。

可以创建启用容器的池，可以带或不带预提取容器映像，如以下示例所示。 可以通过拉取（或预提取）过程从 Docker 中心预加载容器映像，或在 Internet 上预加载另一个容器注册表。 预提取容器映像的优点是，当任务开始运行时，它们不必等待要下载的容器映像。 容器配置在创建池时将容器映像拉取到 VM。 然后，在池中运行的任务即可引用容器映像和容器运行选项列表。



### <a name="pool-without-prefetched-container-images"></a>不带预提取容器映像的池

若要配置启用了容器但不带预提取容器映像的池，请定义 `ContainerConfiguration` 和 `VirtualMachineConfiguration` 对象，如以下示例所示。 此示例和以下示例假定你使用安装了 Docker 引擎的自定义 Ubuntu 16.04 LTS 映像。

```csharp
// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

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

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>容器配置的预提取映像

若要在池上预提取容器映像，请将容器映像 (`containerImageNames`) 列表添加到 `ContainerConfiguration`，并指定映像列表名称。 下面的示例假定你使用自定义 Ubuntu 16.04 LTS 映像，从 [Docker 中心](https://hub.docker.com)预提取 TensorFlow 映像。 本示例包含一个在池节点的 VM 主机中运行的启动任务。 可以在主机中运行具有特定目的（例如，装载可以从容器访问的文件服务器）的启动任务。

```csharp
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

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>从专用容器注册表中预提取映像

另外，也可以通过对专有容器注册表服务器进行身份验证来预提取容器映像。 在以下示例中，`ContainerConfiguration` 和 `VirtualMachineConfiguration` 对象使用自定义 Ubuntu 16.04 LTS 映像，并从专用 Azure 容器注册表中预提取专用 TensorFlow 映像。

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

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>任务容器设置

若要在计算节点上运行容器任务，必须指定特定于容器的设置，如任务运行选项、要使用的映像和注册表。

使用任务类中的 `ContainerSettings` 属性来配置特定于容器的设置。 这些设置由 [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) 类定义。

如果在容器映像上运行任务，[云任务](/dotnet/api/microsoft.azure.batch.cloudtask)和[作业管理器任务](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask)将需要容器设置。 但是，[启动任务](/dotnet/api/microsoft.azure.batch.starttask)、[作业准备任务](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)和[作业发布任务](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)都不需要容器设置（即，它们可以在容器上下文中或直接在节点上运行）。

在配置容器设置时，`AZ_BATCH_NODE_ROOT_DIR` 下的所有目录（节点上 Azure Batch 目录的根）都以递归方式映射到容器，所有任务环境变量都会映射到容器，并在容器中执行任务命令行。

[容器配置的预提取映像](#prefetch-images-for-container-configuration)中的代码示例介绍了如何指定启动任务的容器配置。 下面的代码示例演示如何指定云任务的容器配置：

```csharp
// Simple container task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
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
