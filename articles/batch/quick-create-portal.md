---
title: Azure 快速入门 - 运行 Batch 作业 - 门户
description: 了解如何使用 Azure 门户创建 Batch 帐户、计算节点池以及在池中运行基本任务的作业。
ms.topic: quickstart
ms.date: 07/03/2018
ms.custom: mvc
ms.openlocfilehash: 4857b9ffb38cf48678f793284afaaf3bbefb7e42
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82114133"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>快速入门：在 Azure 门户中运行第一个 Batch 作业

本快速入门介绍如何使用 Azure 门户创建 Batch 帐户、包含计算节点（虚拟机）的*池*以及在池中运行基本*任务*的*作业*。 完成本快速入门以后，你会了解 Batch 服务的重要概念，并可使用更逼真的工作负荷进行更大规模的 Batch 试用。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>登录 Azure 

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-batch-account"></a>创建批处理帐户

请按以下步骤创建一个用于测试的示例 Batch 帐户。 创建池和作业需要 Batch 帐户。 如此处所示，可以将 Azure 存储帐户与 Batch 帐户相关联。 虽然本快速入门不需要，但存储帐户可以用于为大多数现实世界的工作负荷部署应用程序和存储输入和输出数据。


1. 选择“创建资源”   >   “计算” >   “Batch 服务”。 

   ![市场中的批处理][marketplace_portal]

2. 在“帐户名称”和“资源组”中输入值   。 帐户名称必须在所选 Azure **位置**中唯一，只能使用小写字符或数字，包含 3-24 个字符。 

3. 在**存储帐户**中选择一个现有存储帐户，或创建一个新的存储帐户。

4. 让其余设置保留默认值，然后选择“创建”以创建该帐户。 

   ![创建批处理帐户][account_portal]  

出现“部署成功”消息后，转到门户中的 Batch 帐户。 

## <a name="create-a-pool-of-compute-nodes"></a>创建计算节点池

有了 Batch 帐户以后，请创建一个示例池，其中包含用于测试的 Windows 计算节点。 此快速示例的池包含 2 个运行 Windows Server 2012 R2 映像的节点，该映像来自 Azure 市场。


1. 在 Batch 帐户中，选择“池”   >   “添加”。

2. 输入名为“mypool”的**池 ID**  。 

3. 在“操作系统”中选择以下设置（可以浏览其他选项）。 
  
   |设置  |值  |
   |---------|---------|
   |**映像类型**|市场 (Linux/Windows)|
   |**发布者**     |MicrosoftWindowsServer|
   |**产品**     |WindowsServer|
   |**Sku**     |2012-R2-Datacenter-smalldisk|

   ![选择池操作系统][pool_os] 

4. 向下滚动，以便输入“节点大小”和“规模”设置。   就此快速示例来说，建议的节点大小在性能和成本之间达成了很好的平衡。
  
   |设置  |值  |
   |---------|---------|
   |**节点定价层**     |Standard_A1|
   |**目标专用节点**     |2|

   ![选择池大小][pool_size] 

5. 让其余设置保留默认值，然后选择“确定”以创建该池。 

Batch 会立即创建池，但分配和启动计算节点则需要数分钟。 在此期间，池的“分配状态”  为“调整大小”  。 当池在调整大小时，可以继续操作，创建作业和任务。 

![处于“调整大小”状态的池][pool_resizing]

数分钟后，池的状态为“稳定”，节点启动。  选择“节点”，查看节点的状态。  节点在状态为“空闲”时可以运行任务。  

## <a name="create-a-job"></a>创建作业

有了池之后，请创建可在其中运行的作业。 Batch 作业是适用于一个或多个任务的逻辑组。 作业包含任务的公用设置，例如优先级以及运行任务的池。 作业一开始没有任务。 

1. 在 Batch 帐户视图中，选择“作业”   >   “添加”。 

2. 输入名为“myjob”的**作业 ID**  。 在“池”中，选择“mypool”。   其余设置保留默认值，然后选择“确定”  。

   ![创建作业][job_create]

创建作业后，“任务”  页会打开。

## <a name="create-tasks"></a>创建任务

现在创建要在作业中运行的示例任务。 通常创建多个可在计算节点上运行的任务，由 Batch 进行排队和分发。 此示例创建两个相同的任务。 每个任务都运行一个命令行，用于显示计算节点上的 Batch 环境变量，然后等待 90 秒。 

使用 Batch 时，可以在命令行中指定应用或脚本。 Batch 提供多种将应用和脚本部署到计算节点的方式。 

若要创建第一个任务，请执行以下操作：

1. 选择 **添加** 。

2. 输入名为“mytask”的**任务 ID**。  

3. 在“命令行”中输入 `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`。 其余设置保留默认值，然后选择“确定”  。

   ![创建任务][task_create]

创建任务后，Batch 会让其排队在池中运行。 可以使用节点来运行该任务后，任务就会运行。

若要创建第二个任务，请回到步骤 1。 输入另一**任务 ID**，但请指定相同的命令行。 如果第一个任务仍在运行，Batch 会在池中的另一节点上启动第二个任务。

## <a name="view-task-output"></a>查看任务输出

前面的任务示例在数分钟内完成。 若要查看已完成任务的输出，请选择“节点上的文件”  ，然后选择 `stdout.txt` 文件。 此文件显示任务的标准输出。 内容如下所示：

![查看任务输出][task_output]

内容显示在节点上设置的 Azure Batch 环境变量。 创建自己的 Batch 作业和任务时，可以在任务命令行中引用这些环境变量，并在命令行运行的应用和脚本中引用它们。

## <a name="clean-up-resources"></a>清理资源

若要继续学习 Batch 教程和示例，请使用在本快速入门中创建的 Batch 帐户和关联的存储帐户。 Batch 帐户本身不收费。

只要有节点在运行，就会对池收费，即使没有计划作业。 不再需要池时，请将其删除。 在帐户视图中，选择“池”  和池的名称。 然后选择“删除”  。  删除池时会删除节点上的所有任务输出。 

若不再需要资源组、Batch 帐户以及所有相关资源，请将其删除。 为此，请选择 Batch 帐户所在的资源组，然后选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

本快速入门创建了 Batch 帐户、Batch 池和 Batch 作业。 作业运行示例任务，你查看了在其中一个节点上产生的输出。 了解 Batch 服务的重要概念以后，即可使用更逼真的工作负荷进行更大规模的 Batch 试用。 若要详细了解 Azure Batch，请继续学习 Azure Batch 教程。 

> [!div class="nextstepaction"]
> [Azure Batch 教程](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png
