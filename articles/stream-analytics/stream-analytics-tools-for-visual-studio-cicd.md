---
title: 使用流分析工具进行持续集成和开发
description: 本文介绍如何使用用于 Azure 流分析的 Visual Studio 工具设置持续集成和部署过程。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 9/27/2017
ms.openlocfilehash: e4e831c602255df66f4c86ffa17336f51d2b52f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>使用流分析工具进行持续集成和开发
本文介绍如何使用适用于 Visual Studio 的 Azure 流分析工具设置持续集成和部署过程。

使用版本 2.3.0000.0 或更高版本的[适用于 Visual Studio 的流分析工具](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)以获得对 MSBuild 的支持。

有 NuGet 包可用：[Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/)。 它提供了 MSBuild、本地运行和部署工具，用于支持流分析 Visual Studio 项目的持续集成和部署进程。 
> [!NOTE] 
NuGet 包只能与 2.3.0000.0 或以上版本的用于 Visual Studio 的流分析工具配合使用。 如果具有在以前版本的 Visual Studio 工具中创建的项目，只需使用 2.3.0000.0 或以上版本将其打开并保存即可。 然后即可启用新功能。 

有关详细信息，请参阅[适用于 Visual Studio 的流分析工具](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)。

## <a name="msbuild"></a>MSBuild
同标准 Visual Studio MSBuild 体验一样，可通过两种方式生成项目。 可右键单击该项目，然后选择“生成”。 还可从命令行使用 NuGet 包中的 MSBuild。
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

成功生成流分析 Visual Studio 项目后，会在 bin/[Debug/Retail]/Deploy 文件夹下生成以下两个 Azure 资源管理器模板文件： 

*  资源管理器模板文件

       [ProjectName].JobTemplate.json 

*  资源管理器参数文件

       [ProjectName].JobTemplate.parameters.json   

parameters.json 文件中的默认参数来自 Visual Studio 项目中的设置。 如果要部署到其他环境，请相应地替换参数。

> [!NOTE] 
对于所有凭据，默认值均设置为 null。 部署到云之前，必须先设置这些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
深入了解如何[使用资源管理器模板文件和 Azure PowerShell 进行部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)。 深入了解如何 [use an object as a parameter in a Resource Manager template](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)（将对象用作资源管理器模板中的参数）。


## <a name="command-line-tool"></a>命令行工具

### <a name="build-the-project"></a>生成项目
NuGet 包具有一个名为 SA.exe 的命令行工具。 该工具支持在任意计算机上生成项目并进行本地测试，可在持续集成和持续交付进程中使用。 

默认情况下，部署文件位于当前目录下。 可通过使用以下 -OutputPath 参数指定输出路径：

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>在本地测试脚本

如果项目已在 Visual Studio 中指定本地输入文件，则可使用 localrun 命令运行自动化脚本测试。 输出结果位于当前目录下。
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>生成与流分析 PowerShell API 配合使用的作业定义文件

arm 命令将通过内部版本生成的作业模板和作业模板参数文件视为输入。 然后将其合并为可与流分析 PowerShell API 配合使用的作业定义 JSON 文件。

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
示例：
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


