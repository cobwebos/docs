---
title: "如何使用流分析 Visual Studio 工具设置持续集成和部署进程 | Microsoft 文档"
description: "使用流分析 Visual Studio 工具设置持续集成和部署进程的教程"
keywords: visual studio, NuGet, DevOps, CI/CD
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 947266dc94babab21556da26d8cc8c917cf81c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-the-continuous-integration-and-deployment-process"></a>使用流分析 Visual Studio 工具设置持续集成和部署进程
在本教程中，将学习如何使用流分析 Visual Studio 工具设置持续集成和部署进程。

最新版本（2.3.0000.0 或以上）的[适用于 Visual Studio 的流分析工具](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)增加了对 **MSBuild** 的支持。 

此外，还有新发布的 NuGet 包 [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/)。 它提供了 MSBuild、本地运行和部署工具，用于支持流分析 Visual Studio 项目的持续集成和部署进程。 
> [!NOTE] 
NuGet 包只能与 2.3.0000.0 或以上版本的适用于 Visual Studio 的流分析工具配合使用。 如果你有在 Visual Studio 之前版本中创建的项目，只需使用 2.3.0000.0 或以上版本将其打开并保存， 然后，即可启用新功能。 

[了解如何使用适用于 Visual Studio 的流分析工具](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="msbuild"></a>MSBuild
与标准的 Visual Studio MSBuild 体验相同，要生成一个项目，你可以右击项目，然后选择“生成”，或从命令行中使用 NuGet 包中的 **MSBuild**。
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

流分析 Visual Studio 项目成功生成后，会在 **bin/[Debug/Retail]/Deploy** 文件夹下生成以下两个 Azure 资源管理器模板文件。 

Azure 资源管理器模板文件。
*       [ProjectName].JobTemplate.json 

Azure 资源管理器参数文件。
*       [ProjectName].JobTemplate.parameters.json   

parameters.json 文件中的默认参数来自 Visual Studio 项目中的设置。 如果要部署到其他环境，只需相应替换参数即可。 
> [!NOTE] 
对于所有凭据，默认值全部设置为 null。 在部署到云之前，必须对它们进行设置。
```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
了解[如何使用 Azure 资源管理器模板文件和 Azure PowerShell 进行部署](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy)和[如何在 Azure 资源管理器模板中将对象用作参数](https://docs.microsoft.com/en-us/azure/architecture/building-blocks/extending-templates/objects-as-parameters)。


## <a name="command-line-tool"></a>命令行工具

### <a name="build-the-project"></a>生成项目
在 NuGet 包中，有一个名为 **SA.exe** 的命令行工具。 该工具支持项目生成以及在任意计算机上进行本地测试，可在持续集成和持续传递进程中使用。 

默认情况下，部署文件位于当前目录下。 可通过 OutputPath 参数指定输出路径。

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>在本地测试脚本

如果项目已在 Visual Studio 中指定本地输入文件，则可使用 localrun 命令运行自动化脚本测试。 输出结果位于当前目录下。
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-job-definition-file-to-use-with-stream-analytics-power-shell"></a>使用流分析 Power Shell 生成作业定义文件。

arm 命令将通过内部版本生成的作业模板和作业模板参数文件视为输入。 然后将其合并为可与流分析 PowerShell API 配合使用的作业定义 JSON 文件。

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```

```
Example
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


