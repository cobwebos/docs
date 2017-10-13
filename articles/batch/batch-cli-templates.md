---
title: "运行端到端的 Azure Batch 作业而无需编写代码（预览版） | Microsoft Docs"
description: "创建 Azure CLI 的模板文件，以创建 Batch 池、作业和任务。"
services: batch
author: mscurrell
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 07/20/2017
ms.author: markscu
ms.openlocfilehash: 6b91466da46d1f4ca9f25bf1718be783603efc58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>使用 Azure Batch CLI 模板和文件传输（预览版）

使用 Azure CLI 可在不编写代码的情况下运行 Batch 作业。

可通过 Azure CLI 创建和使用模板文件，通过该模板文件可创建 Batch 池、作业和任务。 可轻松将作业输入文件上传到与下载的批处理帐户和作业输出文件相关联的存储帐户。

## <a name="overview"></a>概述

通过 Azure CLI 扩展，非开发者用户可使用端到端的 Batch。 可创建池、上传输入数据、创建作业和相关联的任务，以及下载生成的输出数据 - 无需任何代码，直接使用 CLI 或将 CLI 集成到脚本。

Batch 模板基于 [Azure CLI 中现有的 Batch 支持](https://docs.microsoft.com/azure/batch/batch-cli-get-started#json-files-for-resource-creation)，允许 JSON 文件为池、作业、任务及其他项的创建指定属性值。 通过 Batch 模板，可以在 JSON 文件功能的基础上添加以下功能：

-   可以定义参数。 使用模板时，仅指定参数值以创建项，而在模板正文中指定其他项属性值。 了解 Batch 和 Batch 运行的应用程序的用户可以创建模板、指定池、作业和任务属性值。 不太熟悉 Batch 和/或应用程序的用户只需指定定义的参数的值。

-   使用作业任务工厂可创建与作业相关联的一个或多个任务，而无需创建多个任务定义，因此极大地简化了作业的提交。


需要将输入数据文件应用于作业，且通常会生成输出数据文件。 默认情况下，可使用 CLI 将与每个 Batch 帐户和文件相关联的存储帐户轻松转移到和转移出此存储帐户，无需进行编码，且无需任何存储凭据。

例如，[ffmpeg](http://ffmpeg.org/) 是处理音频和视频文件的常用应用程序。 Azure Batch CLI 可用于调用 ffmpeg，以便将源视频文件转码为不同解决方法。

-   已创建池模板。 创建模板的用户了解如何调用 ffmpeg 应用程序及其需求；他们可指定适当的操作系统、VM 大小、安装 ffmpeg 的方法（例如，从应用程序包或使用包管理器进行安装），以及其他池属性值。 已创建参数，因此当使用模板时，仅需指定池 id 和 VM 数。

-   已创建作业模板。 创建模板的用户了解如何调用 ffmpeg，以便将源视频转码为不同的分辨率，并指定任务命令行；他们还了解存在包含源视频文件（每个输入文件均包含所需任务）的文件夹。

-   具有一组用于转码的视频文件的最终用户首先需要使用池模板创建一个池，然后仅指定池 ID 和所需的 VM 数量。 然后，他们可以上传源文件以进行转码。 可使用作业模板提交作业，仅指定池 id 和上传的源文件的位置。 创建 Batch 作业时，每个输入文件生成一项任务。 最后，将下载已转码的输出文件。

## <a name="installation"></a>安装

模板和文件传输功能需要安装扩展。

有关如何安装 Azure CLI 的说明，请参阅[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

安装 Azure CLI 后，即可使用以下 CLI 命令安装 Batch 扩展：

```azurecli
az component update --add batch-extensions --allow-third-party
```

有关 Batch 扩展的详细信息，请参阅 [Microsoft Azure Batch CLI Extensions for Windows, Mac and Linux](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux)（Windows、Mac 和 Linux 的 Microsoft Azure Batch CLI 扩展）。

## <a name="templates"></a>模板

通过 Azure Batch CLI，可通过指定包含属性名称和值的 JSON 文件创建池、作业和任务等项。 例如：

```azurecli
az batch pool create –-json-file AppPool.json
```

Azure Batch 模板在功能和语法上非常类似于 Azure 资源管理器模板。 它们是包含项属性名称和值的 JSON 文件，但添加了以下主要概念：

-   **参数**

    -   允许在正文部分中指定属性值，使用模板时，仅需提供参数值。 例如，池的完整定义应放入正文且仅定义池 id 的一个参数；因此仅需提供一个池 id 字符串来创建池。
        
    -   模板正文可由了解 Batch 和 Batch 运行的应用程序的人进行创建；使用模板时，必须提供仅作者定义的参数值。 因此，没有深入了解 Batch 和/或应用程序的用户可以使用模板。

-   **变量**

    -   允许在一个位置指定简单或复杂参数值，并在模板正文中的一个或多个位置使用它们。 变量可以简化和减小模板大小，以及通过更改位置的属性（其值可能会更改）使其更易于维护。

-   **更高级别的构造**

    -   Batch API 中尚不可用的模板提供了一些更高级别的构造。 例如，可以在作业模板中定义任务工厂，以使用常见任务定义为作业创建多项任务。 这些构造避免了进行编码以动态创建多个 JSON 文件（如每个任务创建一个文件），以及创建脚本文件以通过程序包管理器安装应用程序等需求。

    -   在某些时候（如果适用），可能会将这些构造添加到 Batch 服务，且在 Batch API、UI 等中可用。

### <a name="pool-templates"></a>池模板

除参数和变量的标准模板功能外，池模板还支持以下更高级别的构造：

-   **包引用**

    -   允许通过使用包管理器将软件复制到池节点（可选）。 已指定包管理器和包 id。 能够声明一个或多个包，便无需创建获取所需包的脚本、安装脚本，以及在每个池节点上运行脚本。

下面是一个模板示例，该示例创建已安装 ffmpeg 的 Linux VM 的池，且仅需提供要使用的池 id 字符串和 VM 数：

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool id "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04.0-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

如果模板文件名为 pool-ffmpeg.json，然后将调用模板，如下所示：

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>作业模板

除参数和变量的标准模板功能外，作业模板还支持以下更高级别的构造：

-   **任务工厂**

    -   通过一个任务定义即可为作业创建多项任务。 支持三种类型的任务工厂 - 参数扫描、每个文件任务和任务集合。

下面是一个模板示例，该示例创建使用 ffmpeg 将 MP4 视频文件转码为两个较低分辨率之一（将创建每个源视频文件的一个任务）的作业：

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

如果模板文件名为 job-ffmpeg.json，然后将调用模板，如下所示：

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>文件组和文件传输

大部分作业和任务均需要输入文件并生成输出文件。 输入文件和输出文件通常需要从客户端传输到节点，或从节点传输到客户端。 Azure Batch CLI 扩展提取文件传输，并利用默认情况下为每个 Batch 帐户创建的存储帐户。

文件组等同于在 Azure 存储帐户中创建的容器。 文件组允许包含子文件夹。

Batch CLI 扩展提供相关的命令，以将客户端的文件上传到指定文件组，并将指定文件组的文件下载到客户端。

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

通过池和作业模板，可将存储在文件组中的文件指定为复制到池节点或离开池节点返回到文件组。 例如，在以前指定的作业模板中，任务工厂的文件组“ffmpeg-input”被指定为复制到节点以供转码的源视频文件的位置；文件组“ffmpeg-output”被用作将已转码的输出文件从运行每个任务的节点上复制到的位置。

## <a name="summary"></a>摘要

目前仅对 Azure CLI 添加了模板和文件传输支持。 目标是向无需使用 Batch API 开发代码的用户（如研究人员、IT 用户等）展开可使用 Batch 的受众。 了解 Azure、Batch 和 Batch 运行的应用程序的用户无需编码即可创建模板以创建池和作业。 通过模板参数，对 Batch 和应用程序没有深入了解的用户也可使用这些模板。

试用 Azure CLI 的 Batch 扩展，并通过本文的评论区或 [Azure Batch 论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)向我们提供任何反馈或建议。

## <a name="next-steps"></a>后续步骤

- 请参阅 Batch 模板博客文章：[Running Azure Batch jobs using the Azure CLI – no code required](https://azure.microsoft.com/en-us/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/)（使用 Azure CLI 运行 Azure Batch 作业 – 无需代码）。
- 有关安装和使用情况的详细文档、示例和源代码，请参阅 [Azure GitHub 存储库](https://github.com/Azure/azure-batch-cli-extensions)。
