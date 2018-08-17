---
title: 使用 Azure Batch 和 Batch Explorer 渲染 Blender 场景
description: 教程 - 如何使用 Azure Batch 和 Batch Explorer 客户端应用程序渲染 Blender 场景中的多个帧
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ms.openlocfilehash: 8df9054e069540398c137290e682bb4160b4a799
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036309"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>教程：使用 Batch Explorer 渲染 Blender 场景

本教程介绍如何渲染 Blender 演示场景的多个帧。 本教程使用 Blender 是因为它免费（不管是客户端还是渲染 VM），但如果使用其他应用程序（例如 Maya 或 3ds Max），则过程十分类似。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 将 Blender 场景上传到 Azure 存储
> * 创建包含多个节点的 Batch 池，以便执行渲染操作
> * 渲染多个帧
> * 查看并下载渲染的帧文件

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

Azure Batch 帐户和关联的存储帐户。  请参阅任何 Batch 快速入门文章（例如 [CLI 文章](https://docs.microsoft.com/azure/batch/quick-create-cli)），了解如何创建 Batch 帐户。

就本教程中指定的 VM 大小和 VM 数目来说，需要至少包含 50 个核心的低优先级核心配额；可以使用默认配额，但必须使用较小的 VM 大小，这意味着图像渲染的时间会变长。 请求提高核心配额的过程详见[此文](https://docs.microsoft.com/azure/batch/batch-quota-limit)。

最后，必须安装适用于 Windows、OSX 和 Linux 的 [Batch Explorer](https://azure.github.io/BatchExplorer/)。 它是可选的，但如果安装了 [Blender](https://www.blender.org/download/)，则可查看示例模型文件。

## <a name="download-the-demo-scene"></a>下载演示场景

从 [Blender 演示文件网页](https://www.blender.org/download/demo-files/)下载适用于 Blender 的“Class room”演示 ZIP 文件，然后将其解压缩到本地驱动器。

## <a name="upload-a-scene-to-azure-storage"></a>将场景上传到 Azure 存储

为演示场景文件创建存储帐户容器：

* 启动 Batch Explorer
* 从左侧的主菜单中，选择“数据”菜单项。
* 确保在下拉菜单中选中“文件组”。
* 选择“+”按钮，创建新的名为“blender-classroom”的“空文件组”
  * 文件组就是带“fgrp-”前缀的 Azure 存储 Blob 容器；它是一种约定，用于筛选掉存储帐户中的其他容器

![场景文件的文件组](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

上传场景文件：

* 在 Batch Explorer 中选择新容器，然后将“classroom”文件夹的内容拖放到该容器中。

![上传的场景文件](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>为输出图像创建 Azure 存储容器

为演示场景输出文件创建存储帐户容器：

* 从左侧的主菜单中，选择“数据”菜单项。
* 选择“+”按钮，创建新的名为“render-output”的“空文件组”

![输出文件的文件组](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>创建用于渲染的 VM 池

使用包含 Blender 应用程序的渲染 Azure 市场 VM 映像创建 Batch 池：

* 从左侧的主菜单中，选择“库”菜单项。
* 从应用程序项列表中选择“Blender”项。
* 选择用于在 Windows Server 上渲染帧的项
* 可以选择项右侧的链接图标，以便查看将要用来创建池和作业的模板文件。

![Blender 库项](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* 选择“创建以后使用的池”按钮
  * 保留池名称“blender-windows”
  * 将“专用 VM 计数”设置为“0”
  * 将“低优先级 VM 计数”设置为“3”
  * 将“节点大小”设置为“Standard_F16”- 可以选择其他 VM 大小，但渲染帧所需的时间将主要取决于核心数。
* 选择用于创建池的绿色按钮
  * 池差不多会立即创建，但分配和启动 VM 需要数分钟。

![用于 Blender 的池模板](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> 请注意，当 VM 出现在池中时，这些 VM 的成本计入 Azure 订阅费用中。 需删除池或 VM 才能让计费停止。 请在本教程结束时删除池，防止持续计费。

池和 VM 的状态可以在“池”视图中监视；以下示例显示所有三个 VM 都已分配，两个已启动并处于空闲状态，一个仍然在启动：![池热度地图](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>创建渲染作业

创建渲染作业，以便使用已创建的池渲染一些帧：
* 从左侧的主菜单中，选择“库”菜单项。
* 从应用程序项列表中选择“Blender”项。
* 选择用于在 Windows Server 上渲染帧的项。
* 选择“使用现有池运行作业”按钮
* 选择“blender-windows”池
* 将“作业名称”设置为“blender-render-tutorial1”
* 选择“fgrp-blender-classroom”作为“输入数据”
* 选择“Blend 文件”的文件图标并选择“classroom.blend”
* 将“帧开始”保留为“1”，将“帧结束”设置为“5”
* 将“输出”设置为“fgrp-render-output”
* 选择用于创建作业的绿色按钮；作业在创建后将包含五个任务，一个任务适用于一个帧

![用于 Blender 的作业模板](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

作业和所有任务都已创建后，作业会与作业任务一起显示：![作业任务的列表](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

当任务开始在池 VM 上首次运行时，系统会运行 Batch 作业准备任务，以便将存储文件组中的场景文件复制到 VM 中供 Blender 访问。
渲染器的状态可以通过查看 Blender 生成的 stdout.txt 日志文件来确定。  选择一个任务，“任务输出”会默认显示，而“stdout.txt”文件则可在选中后查看。
![stdout 文件](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

如果选择“blender-windows”池，则会看到池 VM 处于运行状态：![显示节点正在运行的池热度地图](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

渲染的图像将需要数分钟来生成，具体取决于所选 VM 大小。  使用此前指定的 F16 VM 时，帧的渲染时间大约为 16 分钟。

## <a name="view-the-rendering-output"></a>查看渲染输出

当帧渲染完以后，这些任务会显示为已完成：![完成的任务](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

渲染的图像先写入到 VM 中，选择“wd”文件夹即可查看：![池节点上的已渲染图像](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

作业模板也指定，输出帧和日志文件写回到在创建作业时指定的 Azure 存储帐户文件组。  “数据 UI”可用来查看输出文件和日志，还可用来下载文件：![存储文件组中的已渲染图像](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

当所有任务都已完成后，系统会将作业标记为已完成：![已完成的作业和所有任务](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>清理资源

> [!WARNING]
> 必须删除池（也可将池的大小重设为零节点）才能阻止系统将 VM 费用计入 Azure 订阅。

* 选择“池”
* 选择“blender-windows”池
* 可以在右键单击后选择“删除”，也可选择池上方的垃圾桶图标

## <a name="next-steps"></a>后续步骤
* 通过 Batch Explorer 在“库”部分浏览可用的渲染应用程序。
* 每个应用程序都有多个可用的模板，这些模板会随时间而扩展。  例如，Blender 的模板可将单个图像拆分成多个图块，这样就可以并行渲染图像的各个部分。
* 如需渲染功能的全面介绍，请查看[此处](https://docs.microsoft.com/azure/batch/batch-rendering-service)提供的一系列文章。
