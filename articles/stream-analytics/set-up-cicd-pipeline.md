---
title: 使用 Azure DevOps 为流分析作业创建 CI/CD 管道
description: 本文介绍如何为 Azure DevOps 中的 Azure 流分析作业设置持续集成和部署 (CI/CD) 管道
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: d9b6dfc977aab7d8907b5d3c3851a22f96227d78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757752"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>使用 Azure DevOps 为流分析作业创建 CI/CD 管道

本文介绍如何使用 Azure 流分析 CI/CD 工具创建 Azure DevOps [生成](/azure/devops/pipelines/get-started/pipelines-get-started) 和 [发布](/azure/devops/pipelines/release/define-multistage-release-process) 管道。

## <a name="commit-your-stream-analytics-project"></a>提交流分析项目

在开始之前，请将完整的流分析项目作为源文件提交到 [Azure DevOps](/azure/devops/user-guide/source-control) 存储库。 可以在 Azure Pipelines 中引用此 [示例存储库](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo) 和 [流分析项目源代码](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) 。

本文中的步骤使用流分析 Visual Studio Code 项目。 如果使用的是 Visual Studio 项目，请按照 [使用 CI/CD 工具自动执行 Azure 流分析作业的生成、测试和部署](cicd-tools.md)中的步骤操作。

## <a name="create-a-build-pipeline"></a>创建生成管道

本部分介绍如何创建生成管道。 可以在 Azure DevOps 中引用此示例 [自动生成和测试管道](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) 。

1. 打开 web 浏览器并导航到 Azure DevOps 中的项目。  

1. 在左侧导航菜单中的 " **管道** " 下，选择 " **生成**"。 然后选择 " **新建管道**"。

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目" 和 "存储库"。 然后选择“继续”。

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目" **空作业**"。

## <a name="install-npm-package"></a>安装 npm 包

1. 在 " **任务** " 页上，选择 " **代理作业 1**" 旁边的加号。 在任务搜索中输入 *npm* ，并选择 **npm**。

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目" 中输入以下命令。 保留剩余的默认选项。

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目":::

## <a name="add-a-build-task"></a>添加生成任务

1. 在 " **变量** " 页上，选择 " **+ 添加** " " **管道变量**"。 添加以下变量。 根据您的偏好设置以下值：

   |变量名称|值|
   |-|-|
   |projectRootPath|YourProjectName|
   |outputPath|输出|
   |deployPath|部署|

2. 在 " **任务** " 页上，选择 " **代理作业 1**" 旁边的加号。 搜索 " **命令行**"。

3. 为任务指定 **显示名称** ，然后输入以下脚本。 用存储库名称和项目名称修改脚本。

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   下图使用流分析 Visual Studio Code 项目作为示例。

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目":::

## <a name="add-a-test-task"></a>添加测试任务

1. 在 " **变量** " 页上，选择 " **+ 添加** " " **管道变量**"。 添加以下变量。 将值修改为输出路径和存储库名称。

   |变量名称|值|
   |-|-|
   |Microsoft.vsts.test.testpath|测试|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目" **命令行**"。

3. 为任务指定 **显示名称** ，然后输入以下脚本。 用项目文件名和测试配置文件的路径来修改脚本。 

   有关如何添加和配置测试用例的详细信息，请参阅 [自动测试说明](cicd-tools.md#automated-test) 。

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目":::

## <a name="add-a-copy-files-task"></a>添加复制文件任务

需要添加 "复制文件" 任务，以将测试摘要文件和 Azure 资源管理器模板文件复制到项目文件夹。 

1. 在 " **任务** " 页上，选择 " **+** **代理作业 1**" 旁边的。 搜索 " **复制文件**"。 然后输入以下配置。 通过分配 `**` 到 **内容**，将复制测试结果的所有文件。

   |参数|输入|
   |-|-|
   |显示名称|将文件复制到： $ (artifactstagingdirectory) |
   |源文件夹|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |目录| `**` |
   |目标文件夹| `$(build.artifactstagingdirectory)`|

2. 展开 " **控件选项**"。 **即使之前的任务失败，也请选择，除非**在**运行此任务**时未取消生成。

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目":::

## <a name="add-a-publish-build-artifacts-task"></a>添加 "发布生成项目" 任务

1. 在 " **任务** " 页上，选择 " **代理作业 1**" 旁边的加号。 搜索 " **发布生成项目** "，然后选择带有黑色箭头图标的选项。

2. 展开 " **控件选项**"。 **即使之前的任务失败，也请选择，除非**在**运行此任务**时未取消生成。

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目":::

## <a name="save-and-run"></a>“保存”和“运行”

完成添加 npm 包、命令行、复制文件和发布生成项目任务后，请选择 " **保存 & 队列**"。 出现提示时，请输入 "保存注释"，然后选择 " **保存并运行**"。 你可以从管道的 " **摘要** " 页下载测试结果。

## <a name="check-the-build-and-test-results"></a>检查生成和测试结果

可在 **已发布** 文件夹中找到测试摘要文件和 Azure 资源管理器模板文件。

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目":::

## <a name="release-with-azure-pipelines"></a>Azure Pipelines 发布

本部分介绍如何创建发布管道。 可以在 Azure DevOps 中引用此示例 [发布管道](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo/_release?_a=releases&view=mine&definitionId=2&preserve-view=true) 。

打开 web 浏览器并导航到 Azure 流分析 Visual Studio Code 项目。

1. 在左侧导航菜单中的 " **管道** " 下，选择 " **发布**"。 然后选择 " **新建管道**"。

2. 选择 " **从空作业开始**"。

3. 在 " **项目** " 框中，选择 " **+ 添加项目**"。 在 " **源**" 下，选择创建的生成管道，然后选择 " **添加**"。

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目":::

4. 更改 **阶段 1** 的名称，将 **作业部署到测试环境**。

5. 添加新阶段并将其命名 **为 "生产环境**"。

### <a name="add-deploy-tasks"></a>添加部署任务

1. 从 "任务" 下拉列表中，选择 " **部署作业到测试环境**"。

2. 选择 " **+** **代理作业** " 旁边的，搜索 " **ARM 模板部署**"。 输入以下参数：

   |参数|值|
   |-|-|
   |显示名称| *部署 myASAProject*|
   |Azure 订阅| 选择订阅。|
   |操作| *创建或更新资源组*|
   |资源组| 选择将包含流分析作业的测试资源组的名称。|
   |位置|选择测试资源组的位置。|
   |模板位置| 链接的项目|
   |模板| $ (System.defaultworkingdirectory) /_azure-cicd-部署/删除/myASAProject.JobTemplate.js |
   |模板参数|$ (System.defaultworkingdirectory) /_azure-cicd-部署/删除/myASAProject.JobTemplate.parameters.js |
   |重写模板参数|-<arm_template_parameter> "您的值"。 您可以使用 **变量**定义参数。|
   |部署模式|增量|

3. 从 "任务" 下拉列表中，选择 " **将作业部署到生产环境**"。

4. 选择 " **+** **代理作业** " 旁边的，搜索 " *ARM 模板部署*"。 输入以下参数：

   |参数|值|
   |-|-|
   |显示名称| *部署 myASAProject*|
   |Azure 订阅| 选择订阅。|
   |操作| *创建或更新资源组*|
   |资源组| 选择将包含流分析作业的生产资源组的名称。|
   |位置|选择生产资源组的位置。|
   |模板位置| *链接的项目*|
   |模板| $ (System.defaultworkingdirectory) /_azure-cicd-部署/删除/myASAProject.JobTemplate.js |
   |模板参数|$ (System.defaultworkingdirectory) /_azure-cicd-部署/删除/myASAProject.JobTemplate.parameters.js |
   |重写模板参数|-<arm_template_parameter> "你的值"|
   |部署模式|增量|

### <a name="create-a-release"></a>创建发布

若要创建发布，请在右上角选择 " **创建发布** "。

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="创建新的 Azure 管道&quot;:::

1. 选择 **&quot;使用经典编辑器** 创建无 YAML 的管道&quot;。

1. 选择 &quot;源类型&quot;、&quot;团队项目":::

## <a name="next-steps"></a>后续步骤

* [Azure 流分析持续集成和持续部署](cicd-overview.md)
* [使用 CI/CD 工具自动生成、测试和部署 Azure 流分析作业](cicd-tools.md)