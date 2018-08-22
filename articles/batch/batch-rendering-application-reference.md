---
title: 配合 Azure Batch 使用渲染应用程序
description: 如何配合 Azure Batch 使用渲染应用程序
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 500246dc98618aead11ba539ce4485d25ac62941
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036715"
---
# <a name="rendering-applications"></a>渲染应用程序

可通过创建 Batch 作业和任务来使用渲染应用程序。 任务命令行属性指定相应的命令行和参数。  创建作业任务的最简单方法是使用[此文](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer)中所述的 Batch Explorer 模板。  可以查看这些模板，并根据需要修改创建的版本。

本文简要介绍如何运行每个渲染应用程序。

## <a name="rendering-with-autodesk-3ds-max"></a>使用 Autodesk 3ds Max 进行渲染

### <a name="renderer-support"></a>渲染器支持

除了 3ds Max 中内置的渲染器以外，还可在渲染 VM 映像中使用以下渲染器，并通过 3ds Max 场景文件来引用这些渲染器：

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>任务命令行

调用 `3dsmaxcmdio.exe` 应用程序，在池节点上执行命令行渲染。  运行任务时，此应用程序即会启动。 `3dsmaxcmdio.exe` 应用程序的可用参数与 `3dsmaxcmd.exe` 应用程序相同，具体请参阅 [3ds Max 帮助文档](https://help.autodesk.com/view/3DSMAX/2018/ENU/)（“渲染”|“命令行渲染”部分）。

例如：

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

说明：

* 请务必小心操作，以确保能够找到资产文件。  使用“资产跟踪”窗口来确保路径正确且相关，或者在命令行中使用 `-bitmapPath` 参数。
* 运行任务时检查 3ds Max 写入的 `stdout.txt` 文件，查看渲染器是否有问题，例如，找不到资产。

### <a name="batch-explorer-templates"></a>Batch Explorer 模板

可以从 Batch Explorer 中的“库”访问池和作业模板。  [GitHub 上的 Batch Explorer 数据存储库](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax)中提供了模板源文件。

## <a name="rendering-with-autodesk-maya"></a>使用 Autodesk Maya 进行渲染

### <a name="renderer-support"></a>渲染器支持

除了 Maya 中内置的渲染器以外，还可在渲染 VM 映像中使用以下渲染器，并通过 3ds Max 场景文件来引用这些渲染器：

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>任务命令行

`renderer.exe` 命令行渲染器在任务命令行中使用。 [Maya 帮助](http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4)中介绍了命令行渲染器。

在以下示例中，使用了作业准备任务将场景文件和资产复制到作业准备工作目录，使用了一个输出文件夹来存储渲染图像，并渲染了第 10 帧。

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

对于 V-Ray 渲染，Maya 场景文件通常将 V-Ray 指定为渲染器。  也可以在命令行上指定渲染器：

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

对于 Arnold 渲染，Maya 场景文件通常将 Arnold 指定为渲染器。  也可以在命令行上指定渲染器：

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Batch Explorer 模板

可以从 Batch Explorer 中的“库”访问池和作业模板。  [GitHub 上的 Batch Explorer 数据存储库](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya)中提供了模板源文件。

## <a name="next-steps"></a>后续步骤

通过 Batch Explorer 使用 [GitHub 上的数据存储库](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)中的池和作业模板。  如果需要，请创建新模板，或修改提供的某个模板。