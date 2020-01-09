---
title: 使用 Azure 流分析 CI/CD npm 包
description: 本文介绍如何使用 Azure 流分析 CI/CD npm 包来设置持续集成和部署过程。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ed1f6ebda81a7f036b5e09d15ef4a27323aa9b0d
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660862"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>使用流分析 CI/CD npm 包
本文介绍如何使用 Azure 流分析 CI/CD npm 包来设置持续集成和部署过程。

## <a name="build-the-vs-code-project"></a>生成 VS Code 项目

可以使用**streamanalytics-default-central-us-cicd** npm 包对 Azure 流分析作业启用持续集成和部署。 Npm 包提供了用于生成[流分析 Visual Studio Code 项目](quick-create-vs-code.md)的 Azure 资源管理器模板的工具。 它可以在 Windows、macOS 和 Linux 上使用，而无需安装 Visual Studio Code。

可以直接[下载包](https://www.npmjs.com/package/azure-streamanalytics-cicd)，也可以通过 `npm install -g azure-streamanalytics-cicd` 命令[全局](https://docs.npmjs.com/downloading-and-installing-packages-globally)安装包。 这是建议的方法，也可在**Azure DevOps 管道**中生成管道的 PowerShell 或 Azure CLI 脚本任务中使用。

安装程序包后，请使用以下命令输出 Azure 资源管理器模板。 **ScriptPath**参数是指向项目中的**script.asaql**文件的绝对路径。 确保 asaproj 和 JobConfig 文件与脚本文件位于同一文件夹中。 如果未指定**outputPath** ，则模板将放置在项目的**bin**文件夹下的 "**部署**" 文件夹中。

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
示例（在 macOS 上）
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

当流分析 Visual Studio Code 项目成功生成时，它会在**bin/[Debug/Retail]/Deploy**文件夹下生成以下两个 Azure 资源管理器模板文件： 

*  资源管理器模板文件

       [ProjectName].JobTemplate.json 

*  资源管理器参数文件

       [ProjectName].JobTemplate.parameters.json   

参数. json 文件中的默认参数来自 Visual Studio Code 项目中的设置。 如果要部署到其他环境，请相应地替换参数。

> [!NOTE]
> 对于所有凭据，默认值均设置为 null。 部署到云之前，必须先设置这些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
深入了解如何[使用资源管理器模板文件和 Azure PowerShell 进行部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)。 深入了解如何 [use an object as a parameter in a Resource Manager template](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)（将对象用作资源管理器模板中的参数）。

若要将 Azure Data Lake Store Gen1 的托管标识用作输出接收器，需要在部署到 Azure 之前使用 PowerShell 提供对服务主体的访问权限。 了解有关如何[使用资源管理器模板部署具有托管标识的 ADLS Gen1](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment) 的详细信息。
## <a name="next-steps"></a>后续步骤

* [快速入门：在 Visual Studio Code 中创建 Azure 流分析云作业（预览）](quick-create-vs-code.md)
* [在本地测试流分析查询 Visual Studio Code （预览）](visual-studio-code-local-run.md)
* [了解 Visual Studio Code （预览版）的 Azure 流分析](visual-studio-code-explore-jobs.md)
