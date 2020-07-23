---
title: 通过 CI/CD 和 Azure DevOps 部署 Azure 流分析作业
description: 本文介绍了如何使用 Azure DevOps Services 通过 CI/CD 部署流分析作业。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d9360ff64206cdce208f9643cf8ca86515aaeb7e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75354439"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>教程：使用 Azure Pipelines 通过 CI/CD 部署 Azure 流分析作业
本教程介绍了如何使用 Azure 管道为 Azure 流分析作业设置持续集成和部署。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 向项目中添加源代码管理
> * 在 Azure 管道中创建生成管道
> * 在 Azure 管道中创建发布管道
> * 自动部署和升级应用程序

## <a name="prerequisites"></a>先决条件
在开始之前，请确保具有以下各项：

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 安装 [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 以及“Azure 开发”或“数据存储和处理”工作负荷。  
* [在 Visual Studio 中创建流分析项目](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs)。
* 创建一个 [Azure DevOps](https://visualstudio.microsoft.com/team-services/) 组织。

## <a name="configure-nuget-package-dependency"></a>配置 NuGet 包依赖项
若要在任意计算机上执行自动生成和自动部署，需要使用 NuGet 包 `Microsoft.Azure.StreamAnalytics.CICD`。 它提供了 MSBuild、本地运行和部署工具，用于支持流分析 Visual Studio 项目的持续集成和部署进程。 有关详细信息，请参阅[流分析 CI/CD 工具](stream-analytics-tools-for-visual-studio-cicd.md)。

将 **packages.config** 添加到项目目录。

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>将你的 Visual Studio 解决方案共享到一个新的 Azure Repos Git 存储库

将你的应用程序源文件共享到 Azure DevOps 中的一个项目，以便可以生成内部版本。  

1. 通过在 Visual Studio 的右下角的状态栏中依次选择“添加到源代码管理”和“Git”，为项目创建一个新的本地 Git 存储库。   

2. 在“团队资源管理器”中的“同步”视图中，在“推送到 Azure DevOps Services”下选择“发布 Git 存储库”按钮。    

   ![“推送到 Azure DevOps Services”下的“发布 Git 存储库”按钮](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-git-repo-devops.png)

3. 验证你的电子邮件地址并在“Azure DevOps Services 域”下拉列表中选择你的组织。  输入你的存储库名称并选择“发布存储库”。 

   ![“推送 Git 存储库”下的“发布存储库”按钮](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-repository-devops.png)

    发布存储库会在你的组织中创建一个与本地存储库同名的新项目。 若要在现有项目中创建存储库，请单击“存储库名称”旁边的“高级”并选择一个项目。   可以通过选择“在 Web 上查看”，在浏览器中查看代码。 
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>配置使用 Azure DevOps 的持续交付
Azure Pipelines 生成管道描述了由按顺序执行的生成步骤组成的工作流。 请详细了解 [Azure Pipelines 生成管道](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav)。 

Azure Pipelines 发布管道描述了将应用程序程序包部署到群集的工作流。 一起使用时，生成管道和发布管道将执行从开始到结束的整个工作流，即一开始只有源文件，结束时群集中会有一个运行的应用程序。 请详细了解 Azure Pipelines [发布管道](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)。

### <a name="create-a-build-pipeline"></a>创建生成管道
打开 Web 浏览器并导航到刚刚在 [Azure DevOps](https://app.vsaex.visualstudio.com/) 中创建的项目。 

1. 在“生成和发布”选项卡下，依次选择“生成”、“+新建”。     依次选择“Azure DevOps Services Git”  和“继续”。 
    
    ![在 Azure DevOps 中选择“DevOps Git”源](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source-devops.png)

2. 在“选择模板”中单击“空流程”，从空管道开始。  
    
    ![从 DevOps 中的模板选项中选择空进程](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template-empty-process.png)

3. 在“触发器”下，选中“启用持续集成”触发器状态来启用持续集成。    选择“保存并排队”  以手动启动生成。 
    
    ![启用持续集成触发器状态](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger-status-ci.png)

4. 推送或签入时也会触发生成。 若要检查生成进度，请切换到“生成”选项卡  。在验证生成成功执行后，必须定义用于将应用程序部署到群集的发布管道。 右键单击生成管道旁边的省略号，并选择“编辑”。 

5.  在“任务”中，输入“Hosted”作为**代理队列**。 
    
    ![在“任务”菜单中选择代理队列](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue-task.png) 

6. 在“阶段 1”中，单击 **+** 并添加“NuGet”任务。  
    
    ![在“代理”队列中添加 NuGet 任务](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-nuget-task.png)

7. 展开“高级”，将 `$(Build.SourcesDirectory)\packages` 添加到“目标目录”。   保留剩余的默认 NuGet 配置值。

   ![配置 NuGet 还原任务](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-restore-config.png)

8. 在“阶段 1”中，单击 **+** 并添加“MSBuild”任务。  

   ![在“代理”队列中添加 MSBuild 任务](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-msbuild-task.png)

9. 将“MSBuild 参数”更改为： 

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![在 DevOps 中配置 MSBuild 任务](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-config-msbuild-task.png)

10. 在“阶段 1”中，单击 **+** 并添加“Azure 资源组部署”任务。   
    
    ![添加“Azure 资源组部署”任务](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-resource-group-deployment.png)

11. 展开“Azure 详细信息”并在配置中填写以下信息： 
    
    |**设置**  |**建议的值**  |
    |---------|---------|
    |订阅  |  选择订阅。   |
    |操作  |  创建或更新资源组   |
    |资源组  |  输入资源组名称。   |
    |模板  | [解决方案路径]\bin\Debug\Deploy\\[项目名称].JobTemplate.json   |
    |模板参数  | [解决方案路径]\bin\Debug\Deploy\\[项目名称].JobTemplate.parameters.json   |
    |重写模板参数  | 在文本框中键入重写的模板参数。 例如，–storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre)。 此属性是可选的，但如果不重写密钥参数，则生成会导致错误。    |
    
    ![为 Azure 资源组部署设置属性](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deployment-properties.png)

12. 单击“保存和排队”以测试生成管道  。
    
    ![在 DevOps 中保存生成并将其排入队列](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-and-queue-build.png)

### <a name="failed-build-process"></a>失败的生成过程
如果在生成管道的“Azure 资源组部署”任务中未重写模板参数，可能会收到 null 部署参数的错误。  请返回到生成管道，重写 null 参数来解决错误。

   ![DevOps 流分析生成过程失败](./media/stream-analytics-tools-visual-studio-cicd-vsts/devops-build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>提交并推送更改以触发发布
通过将一些代码更改签入到 Azure DevOps 来验证持续集成管道是否正常工作。    

在编写代码时，Visual Studio 会自动跟踪代码更改。 通过从右下角的状态栏中选择“挂起的更改”图标，将更改提交到本地 Git 存储库。

1. 在“团队资源管理器”中的“更改”视图中，添加一条消息来说明你的更新，然后提交更改。 

    ![从 Visual Studio 提交存储库更改](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-commit-changes-visual-studio.png)

2. 在“团队资源管理器”中选择“未发布的更改”状态栏图标或“同步”视图。 选择“推送”以更新 Azure DevOps 中的代码。 

    ![从 Visual Studio 发布更改](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-visual-studio.png)

将更改推送到 Azure DevOps Services 会自动触发生成。  当生成管道成功完成时，会自动创建一个发布，并开始更新群集上的作业。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、流式处理作业以及所有相关资源，请将其删除。 删除作业可避免对作业使用的流单元进行计费。 如果计划在将来使用该作业，可以先停止它，等到以后需要时再重启它。 如果你不打算继续使用此作业，请使用以下步骤删除本教程中创建的所有资源：

1. 在 Azure 门户的左侧菜单中，单击“资源组”  ，并单击已创建资源的名称。  
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。  

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Visual Studio 的 Azure 流分析工具设置持续集成和部署过程，请继续阅读有关设置 CI/CD 管道的文章：

> [!div class="nextstepaction"]
> [使用流分析工具进行持续集成和开发](stream-analytics-tools-for-visual-studio-cicd.md)
