---
title: 持续集成和开发与 Azure Stream Analytics CI/CD npm 包
description: 本文介绍如何使用 Azure Stream Analytics CI/CD npm 包来设置持续集成和部署过程。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: fa5a57afa379c6bbe027be80f400fc176800d289
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158510"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>持续集成和开发与 Stream Analytics CI/CD npm 包
本文介绍如何使用 Azure Stream Analytics CI/CD npm 包来设置持续集成和部署过程。

## <a name="build-the-vs-code-project"></a>生成的 VS 代码项目

可以启用持续集成和部署为使用 Azure Stream Analytics 作业**asa streamanalytics cicd** npm 包。 Npm 包提供的工具生成的 Azure 资源管理器模板[Stream Analytics Visual Studio Code 项目](quick-create-vs-code.md)。 无需安装 Visual Studio Code，可以在 Windows、 macOS 和 Linux 上使用它。

一旦您有[下载包](https://www.npmjs.com/package/azure-streamanalytics-cicd)，使用以下命令输出的 Azure 资源管理器模板。 **ScriptPath**参数是绝对路径**asaql**项目文件中的。 请确保 asaproj.json 和 JobConfig.json 文件是包含脚本文件所在的文件夹中。 如果**outputPath**未指定，则模板将被放入**部署**下的项目文件夹**bin**文件夹。

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
示例 （macOS 上）
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Stream Analytics Visual Studio Code 项目成功生成，当它生成以下两个 Azure 资源管理器模板文件下的**bin / [Debug/Retail] /deploy**文件夹： 

*  资源管理器模板文件

       [ProjectName].JobTemplate.json 

*  资源管理器参数文件

       [ProjectName].JobTemplate.parameters.json   

Parameters.json 文件中的默认参数是从你的 Visual Studio 代码项目中的设置。 如果要部署到其他环境，请相应地替换参数。

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

* [快速入门：在 Visual Studio Code （预览版） 中创建 Azure Stream Analytics 云作业](quick-create-vs-code.md)
* [测试使用 Visual Studio Code （预览版） 在本地的 Stream Analytics 查询](vscode-local-run.md)
* [了解 Azure Stream Analytics 使用 Visual Studio Code （预览版）](vscode-explore-jobs.md)
