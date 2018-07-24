---
title: 有关使用 VSTS 通过 CI/CD 部署 Azure 流分析作业的教程
description: 本文介绍如何使用 VSTS 通过 CI/CD 部署流分析作业。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: 303c1cfaf2b91712f706c5b78e027bb02739c770
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39074099"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>教程：使用 VSTS 通过 CI/CD 部署 Azure 流分析作业
本教程介绍如何使用 Visual Studio Team Services 为 Azure 流分析作业设置持续集成和部署。 

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 向项目中添加源代码管理
> * 在 Team Services 中创建生成定义
> * 在 Team Services 中创建发布定义
> * 自动部署和升级应用程序

## <a name="prerequisites"></a>先决条件
在开始之前，请确保具有以下各项：

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 安装 [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 以及“Azure 开发”或“数据存储和处理”工作负荷。
* [在 Visual Studio 中创建流分析项目](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-vs)。
* 创建 [Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) 帐户。

## <a name="configure-nuget-package-dependency"></a>配置 NuGet 包依赖项
若要在任意计算机上执行自动生成和自动部署，需要使用 NuGet 包 `Microsoft.Azure.StreamAnalytics.CICD`。 它提供了 MSBuild、本地运行和部署工具，用于支持流分析 Visual Studio 项目的持续集成和部署进程。 有关详细信息，请参阅[流分析 CI/CD 工具](stream-analytics-tools-for-visual-studio-cicd.md)。

将 **packages.config** 添加到项目目录。

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>将 Visual Studio 解决方案共享到一个新的 Team Services Git 存储库
将应用程序源文件共享到 Team Services 中的一个团队项目，以便可以生成内部版本。  

1. 通过在 Visual Studio 的右下角的状态栏中依次选择“添加到源代码管理”和“Git”，为项目创建一个新的本地 Git 存储库。 

2. 在“团队资源管理器”中的“同步”视图中，在“推送到 Visual Studio Team Services”下选择“发布 Git 存储库”按钮。

   ![推送 Git 存储库](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. 验证你的电子邮件地址并在“Team Services 域”下拉列表中选择你的帐户。 输入你的存储库名称并选择“发布存储库”。

   ![推送 Git 存储库](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    发布存储库会在你的帐户中创建一个与本地存储库同名的新团队项目。 若要在现有团队项目中创建存储库，请单击“存储库名称”旁边的“高级”并选择一个团队项目。 可以通过选择“在 Web 上查看”，在浏览器中查看代码。
 
## <a name="configure-continuous-delivery-with-vsts"></a>配置使用 VSTS 的持续交付
Team Services 生成定义所描述的工作流由一些按顺序执行的生成步骤组成。 详细了解 [Team Services 生成定义](https://www.visualstudio.com/docs/build/define/create)。 

Team Services 发布定义描述了将应用程序程序包部署到群集的工作流。 一起使用时，生成定义和发布定义将执行从开始到结束的整个工作流，即一开始只有源文件，结束时群集中会有一个运行的应用程序。 详细了解 Team Services [发布定义](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)。

### <a name="create-a-build-definition"></a>创建生成定义
打开 Web 浏览器并导航到刚刚在 [Visual Studio Team Services](https://app.vsaex.visualstudio.com/) 中创建的团队项目。 

1. 在“生成和发布”选项卡下，依次选择“生成”、“+新建”。  依次选择“VSTS Git”、“继续”。
    
    ![选择源](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. 在“选择模板”中单击“空进程”，从空定义开始。
    
    ![选择“生成模板”](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. 在“触发器”下，选中“启用持续集成”触发器状态来启用持续集成。  选择“保存并排队”以手动启动生成。 
    
    ![触发器状态](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. 推送或签入时也会触发生成。 若要检查生成进度，请切换到“生成”选项卡。在验证生成成功执行后，必须定义用于将应用程序部署到群集的发布定义。 右键单击生成定义旁边的省略号，并选择“编辑”。

5.  在“任务”中，输入“Hosted”作为**代理队列**。
    
    ![选择代理队列](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. 在“阶段 1”中，单击 **+** 并添加“NuGet”任务。
    
    ![添加 NuGet 任务](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. 展开“高级”，将 `$(Build.SourcesDirectory)\packages` 添加到“目标目录”。 保留剩余的默认 NuGet 配置值。

   ![配置 NuGet 任务](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. 在“阶段 1”中，单击 **+** 并添加“MSBuild”任务。

   ![添加 MSBuild 任务](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. 将“MSBuild 参数”更改为：

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![配置 MSBuild 任务](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. 在“阶段 1”中，单击 **+** 并添加“Azure 资源组部署”任务。 
    
    ![添加“Azure 资源组部署”任务](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. 展开“Azure 详细信息”并在配置中填写以下信息：
    
    |**设置**  |建议的值  |
    |---------|---------|
    |订阅  |  选择订阅。   |
    |操作  |  创建或更新资源组   |
    |资源组  |  输入资源组名称。   |
    |模板  | [解决方案路径]\bin\Debug\Deploy\\[项目名称].JobTemplate.json   |
    |模板参数  | [解决方案路径]\bin\Debug\Deploy\\[项目名称].JobTemplate.parameters.json   |
    |重写模板参数  | 在文本框中键入重写的模板参数。 例如，–storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre)。 此属性是可选的，但如果不重写密钥参数，则生成会导致错误。    |
    
    ![设置属性](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. 单击“保存和排队”以测试生成定义。
    
    ![设置重写参数](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>失败的生成过程
如果在生成定义的“Azure 资源组部署”任务中未重写模板参数，可能会收到 null 部署参数的错误。 请返回生成定义，重写 null 参数来解决错误。

   ![生成过程失败](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>提交并推送更改以触发发布
通过将一些代码更改签入到 Team Services 来验证持续集成管道是否正常工作。    

在编写代码时，Visual Studio 会自动跟踪代码更改。 通过从右下角的状态栏中选择“挂起的更改”图标，将更改提交到本地 Git 存储库。

1. 在“团队资源管理器”中的“更改”视图中，添加一条消息来说明你的更新，然后提交更改。

    ![提交和推送更改](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. 在“团队资源管理器”中选择“未发布的更改”状态栏图标或“同步”视图。 选择“推送”以更新 Team Services/TFS 中的代码。

    ![提交和推送更改](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

将更改推送到 Team Services 会自动触发生成。  当生成定义成功完成时，会自动创建一个发布，并开始更新群集上的作业。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、流式处理作业以及所有相关资源，请将其删除。 删除作业可避免对作业使用的流单元进行计费。 如果计划在将来使用该作业，可以先停止它，等到以后需要时再重启它。 如果你不打算继续使用此作业，请使用以下步骤删除本教程中创建的所有资源：

1. 在 Azure 门户的左侧菜单中，单击“资源组”，并单击已创建资源的名称。  
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Visual Studio 的 Azure 流分析工具设置持续集成和部署过程，请继续阅读有关设置 CI/CD 管道的文章：

> [!div class="nextstepaction"]
> [使用流分析工具进行持续集成和开发](stream-analytics-tools-for-visual-studio-cicd.md)