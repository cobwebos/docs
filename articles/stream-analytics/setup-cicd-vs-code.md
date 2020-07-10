---
title: 使用 CI/CD npm 包部署 Azure 流分析作业
description: 本文介绍如何使用 Azure 流分析 CI/CD npm 包设置持续集成和部署过程。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 75db20bdb746e7d15bef56ce7ac0a064993d3f3a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187755"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>使用 CI/CD npm 包部署 Azure 流分析作业 

可以使用 Azure 流分析 CI/CD npm 包为流分析作业设置持续的集成和部署过程。 本文介绍通常如何在任何 CI/CD 系统中使用 npm 包，以及使用 Azure Pipelines 进行部署的具体说明。

有关使用 Powershell 进行部署的详细信息，请参阅[使用资源管理器模板文件进行部署和 Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)。 你还可以了解有关如何[使用对象作为资源管理器模板中的参数的](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)详细信息。

## <a name="build-the-vs-code-project"></a>生成 VS Code 项目

可使用 **asa-streamanalytics-cicd** npm 包启用 Azure 流分析作业的持续集成和部署。 npm 包提供了用于生成[流分析 Visual Studio Code 项目](quick-create-vs-code.md)的 Azure 资源管理器模板的工具。 它可以在 Windows、macOS 和 Linux 上使用，而无需安装 Visual Studio Code。

可以直接[下载包](https://www.npmjs.com/package/azure-streamanalytics-cicd)，也可以通过命令[全局](https://docs.npmjs.com/downloading-and-installing-packages-globally)安装包 `npm install -g azure-streamanalytics-cicd` 。 这是建议的方法，也可在**Azure Pipelines**中生成管道的 PowerShell 或 Azure CLI 脚本任务中使用。

安装包后，请使用以下命令输出 Azure 资源管理器模板。 **scriptPath** 参数是项目中 **asaql** 文件的绝对路径。 请确保 asaproj.json 和 JobConfig.json 文件与脚本文件位于同一文件夹中。 如果未指定 **outputPath**，则模板将放置在项目的 **bin** 文件夹下的 **Deploy** 文件夹中。

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
示例（在 macOS 上）
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

成功生成流分析 Visual Studio Code 项目后，会在 bin/[Debug/Retail]/Deploy 文件夹下生成以下两个 Azure 资源管理器模板文件****： 

* 资源管理器模板文件

   `[ProjectName].JobTemplate.json`

* 资源管理器参数文件

   `[ProjectName].JobTemplate.parameters.json`   

parameters.json 文件中的默认参数来自 Visual Studio Code 项目中的设置。 如果要部署到其他环境，请相应地替换参数。

> [!NOTE]
> 对于所有凭据，默认值均设置为 null。 部署到云之前，必须先设置这些值****。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>使用 Azure Pipelines 进行部署

本部分详细介绍了如何使用 npm 创建 Azure Pipelines[生成](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav)和[发布](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)管道。

打开 web 浏览器并导航到 Azure 流分析 Visual Studio Code 项目。

1. 在左侧导航菜单中的 "**管道**" 下，选择 "**生成**"。 然后选择 "**新建管道**"

   ![创建新的 Azure 管道](./media/setup-cicd-vs-code/new-pipeline.png)

2. 选择 **"使用经典编辑器**创建无 YAML 的管道"。

3. 选择 "源类型"、"团队项目" 和 "存储库"。 然后选择“继续”。

   ![选择 Azure 流分析项目](./media/setup-cicd-vs-code/select-repo.png)

4. 在 "**选择模板**" 页上，选择 "**空作业**"。

### <a name="add-npm-task"></a>添加 npm 任务

1. 在 "**任务**" 页上，选择 "**代理作业 1**" 旁边的加号。 在任务搜索中输入 "npm"，并选择 " **npm**"。

   ![选择 npm 任务](./media/setup-cicd-vs-code/search-npm.png)

2. 为任务指定**显示名称**。 将**命令**选项更改为 "*自定义*"，然后在 "**命令和参数**" 中输入以下命令。 保留剩余的默认选项。

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![输入 npm 任务的配置](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>添加命令行任务

1. 在 "**任务**" 页上，选择 "**代理作业 1**" 旁边的加号。 搜索 "**命令行**"。

2. 为任务指定**显示名称**，然后输入以下脚本。 用存储库名称和项目名称修改脚本。

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![为命令行任务输入配置](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>添加复制文件任务

1. 在 "**任务**" 页上，选择 "**代理作业 1**" 旁边的加号。 搜索 "**复制文件**"。 然后输入以下配置。

   |参数|输入|
   |-|-|
   |显示名称|将文件复制到： $ (artifactstagingdirectory) |
   |源文件夹|`$(system.defaultworkingdirectory)`| 
   |目录| `**\Deploy\**` |
   |目标文件夹| `$(build.artifactstagingdirectory)`|

   ![输入复制任务的配置](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>添加 "发布生成项目" 任务

1. 在 "**任务**" 页上，选择 "**代理作业 1**" 旁边的加号。 搜索 "**发布生成项目**"，然后选择带有黑色箭头图标的选项。 

2. 不要更改任何默认配置。

### <a name="save-and-run"></a>“保存”和“运行”

完成添加 npm、命令行、复制文件和发布生成项目任务后，请选择 "**保存 & 队列**"。 出现提示时，请输入 "保存注释"，然后选择 "**保存并运行**"。

## <a name="release-with-azure-pipelines"></a>Azure Pipelines 发布

打开 web 浏览器并导航到 Azure 流分析 Visual Studio Code 项目。

1. 在左侧导航菜单中的 "**管道**" 下，选择 "**发布**"。 然后选择 "**新建管道**"。

2. 选择 "**从空作业开始**"。

3. 在 "**项目**" 框中，选择 " **+ 添加项目**"。 在 "**源**" 下，选择刚创建的生成管道，然后选择 "**添加**"。

   ![输入生成管道项目](./media/setup-cicd-vs-code/build-artifact.png)

4. 更改**阶段 1**的名称，将**作业部署到测试环境**。

5. 添加新阶段并将其命名**为 "生产环境**"。

### <a name="add-tasks"></a>添加任务

1. 从 "任务" 下拉列表中，选择 "**部署作业到测试环境**"。 

2. 选择 " **+** **代理作业**" 旁边的，搜索 " *Azure 资源组部署*"。 输入以下参数：

   |设置|值|
   |-|-|
   |显示名称| *部署 myASAJob*|
   |Azure 订阅| 选择订阅。|
   |操作| *创建或更新资源组*|
   |资源组| 选择将包含流分析作业的测试资源组的名称。|
   |位置|选择测试资源组的位置。|
   |模板位置| *链接的项目*|
   |模板| $ (ArtifactStagingDirectory) # B0 on |
   |模板参数| ($ (ArtifactStagingDirectory) # B0 on|
   |重写模板参数|-Input_IoTHub1_iotHubNamespace $ (test_eventhubname) |
   |部署模式|增量|

3. 从 "任务" 下拉列表中，选择 "**将作业部署到生产环境**"。

4. 选择 " **+** **代理作业**" 旁边的，搜索 " *Azure 资源组部署*"。 输入以下参数：

   |设置|值|
   |-|-|
   |显示名称| *部署 myASAJob*|
   |Azure 订阅| 选择订阅。|
   |操作| *创建或更新资源组*|
   |资源组| 选择将包含流分析作业的生产资源组的名称。|
   |位置|选择生产资源组的位置。|
   |模板位置| *链接的项目*|
   |模板| $ (ArtifactStagingDirectory) # B0 on |
   |模板参数| ($ (ArtifactStagingDirectory) # B0 on|
   |重写模板参数|-Input_IoTHub1_iotHubNamespace $ (eventhubname) |
   |部署模式|增量|

### <a name="create-release"></a>创建发布

若要创建发布，请在右上角选择 "**创建发布**"。

![使用 Azure Pipelines 创建发布](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>其他资源

若要将 Azure Data Lake Store Gen1 的托管标识用作输出接收器，需要在部署到 Azure 之前使用 PowerShell 提供对服务主体的访问权限。 了解有关如何[使用资源管理器模板部署具有托管标识的 ADLS Gen1](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment) 的详细信息。


## <a name="next-steps"></a>后续步骤

* [快速入门：在 Visual Studio Code (预览中创建 Azure 流分析云作业) ](quick-create-vs-code.md)
* [使用 Visual Studio Code（预览版）在本地测试流分析查询](visual-studio-code-local-run.md)
* [使用 Visual Studio Code（预览版）浏览 Azure 流分析](visual-studio-code-explore-jobs.md)
