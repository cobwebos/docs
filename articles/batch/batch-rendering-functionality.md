---
title: Batch 渲染功能
description: Azure Batch 中的特定渲染功能
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 3f2fa055af63ab3920fec79ad93c25b8ea7e5ed9
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2018
ms.locfileid: "42140598"
---
# <a name="azure-batch-rendering-capabilities"></a>Azure Batch 的渲染功能

标准 Azure Batch 功能用于运行渲染工作负荷与应用程序。 Batch 还包含用于支持渲染工作负荷的特定功能。

有关 Batch 概念的概述，包括池、作业和任务，请参阅[此文](https://docs.microsoft.com/azure/batch/batch-api-basics)。

## <a name="batch-pools"></a>Batch 池

### <a name="rendering-application-installation"></a>渲染应用程序安装

如果只需使用预装的应用程序，则可以在池配置中指定 Azure 市场渲染 VM 映像。

有一个 Windows 2016 映像和一个 CentOS 映像。  在 [Azure 市场](https://azuremarketplace.microsoft.com)中，可以通过搜索“batch 渲染”找到 VM 映像。

有关示例池配置，请参阅 [Azure CLI 渲染教程](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)。  Azure 门户和 Batch Explorer 提供了 GUI 工具用于在创建池时选择渲染 VM 映像。  如果使用 Batch API，请在创建池时，为 [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) 指定以下属性值：

| 发布者 | 产品/服务 | SKU | 版本 |
|---------|---------|---------|--------|
| 批处理 | rendering-centos73 | 呈现 | 最新 |
| 批处理 | rendering-windows2016 | 呈现 | 最新 |

如果池 VM 上需要其他应用程序，则可以使用其他选项：

* 基于标准市场映像的自定义映像：
  * 可以使用此选项为 VM 配置所需的具体应用程序和版本。 有关详细信息，请参阅[使用自定义映像创建虚拟机池](https://docs.microsoft.com/azure/batch/batch-custom-images)。 Autodesk 和 Chaos Group 已分别修改了 Arnold 和 V-Ray，可以验证 Azure Batch 许可服务。 请确保这些应用程序的版本提供此支持，否则，即用即付许可模式将不适用。 运行无头模式（批处理/命令行模式）时，最新版本的 Maya 或 3ds Max 不需要许可证服务器。 如果不确定如何使用此选项，请联系 Azure 支持部门。
* [应用程序包](https://docs.microsoft.com/azure/batch/batch-application-packages)：
  * 使用一个或多个 ZIP 文件打包应用程序文件，通过 Azure 门户上传，然后在池配置中指定该包。 创建池 VM 时，将下载 ZIP 文件并解压缩文件。
* 资源文件：
  * 应用程序文件将上传到 Azure Blob 存储；在[池启动任务](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)中指定文件引用。 创建池 VM 时，会将资源文件下载到每个 VM。

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>预装应用程序的即用即付许可

需在池配置中指定要使用的并且会产生许可费的应用程序。

* [创建池](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body)时指定 `applicationLicenses` 属性。  可在字符串数组中指定以下值 -“vray”、“arnold”、“3dsmax”、“maya”。
* 指定一个或多个应用程序时，这些应用程序的费用将与 VM 费用相加。  [Azure Batch 定价页面](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)上列出了应用程序价格。

可以使用 Azure 门户或 Batch Explorer 选择应用程序和显示应用程序价格。

如果尝试使用某个应用程序，但尚未在池配置的 `applicationLicenses` 属性中指定该应用程序，则应用程序执行将会失败，并出现许可错误和非零退出代码。

### <a name="environment-variables-for-pre-installed-applications"></a>预装应用程序的环境变量

若要为渲染任务创建命令行，必须指定渲染应用程序可执行文件的安装位置。  Azure 市场 VM 映像中已创建系统环境变量，可以改用这些环境变量，而无需指定实际的路径。  这些环境变量补充了为每个任务创建的[标准 Batch 环境变量](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables)。

|Application|应用程序可执行文件|环境变量|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Chaos Group V-Ray Standalone|vray.exe|VRAY_3.60.4_EXEC|
Arnold 2017 命令行|kick.exe|ARNOLD_2017_EXEC|
|Arnold 2018 命令行|kick.exe|ARNOLD_2018_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Azure VM 系列

与其他工作负荷一样，渲染应用程序的系统要求和性能要求根据作业与项目的不同而异。  Azure 中根据要求提供了多种不同的 VM 系列 - 最低成本、最高性价比、最佳性能，等等。
有些渲染应用程序（例如 Arnold）基于 CPU，而有些（例如 V-Ray 和 Blender Cycles）则可以使用 CPU 和/或 GPU。
有关可用 VM 系列和 VM 大小的说明，请参阅 [VM 类型和大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。

### <a name="low-priority-vms"></a>低优先级 VM

与其他工作负荷一样，可在 Batch 池中利用低优先级 VM 进行渲染。  低优先级 VM 的表现与普通的专用 VM 相同，但利用盈余的 Azure 容量，并可享受较大的折扣。  使用低优先级虚拟机的代价是这些虚拟机可能不可用，并将其分配，或在任何时间，具体取决于可用的容量可能会被抢占。 出于此原因，低优先级 VM 并不适合所有渲染作业。 例如，如果需要几个小时才能渲染图像，则这些图像的渲染可能会中断或重启，因为抢占 VM 是不可接受的。

若要详细了解优先级 VM 的特征以及使用 Batch 对其进行配置的各种方法，请参阅[将低优先级 VM 与 Batch 配合使用](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)。

## <a name="jobs-and-tasks"></a>作业和任务

作业和任务不需要特定于渲染的支持。  主要配置项是任务命令行，它需要引用所需的应用程序。
使用 Azure 市场 VM 映像时，最好是使用环境变量来指定路径和应用程序可执行文件。

## <a name="next-steps"></a>后续步骤

有关 Batch 渲染的示例，请尝试学习以下两篇教程：

* [使用 Azure CLI 进行渲染](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [使用 Batch Explorer 进行渲染](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
