---
title: 持续集成和持续部署 - Azure IoT Edge | Microsoft Docs
description: 设置持续集成和持续部署 - Azure IoT Edge 以及 Azure DevOps、Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 85f77d1132af63681ee92cfd2bde82a71d8ed999
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457243"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>向 Azure IoT Edge 进行持续集成和持续部署

借助 Azure Pipelines 中的内置 Azure IoT Edge 任务，可以轻松地在 Azure IoT Edge 应用程序中采用 DevOps。 本文演示了如何使用 Azure Pipelines 的持续集成和持续部署功能，快速高效地生成和测试应用程序并将其部署到 Azure IoT Edge。 

![示意图 - 用于开发和生产的 CI 和 CD 分支](./media/how-to-ci-cd/cd.png)

在本文中，你将学习如何使用 Azure Pipelines 的内置 Azure IoT Edge 任务为 IoT Edge 解决方案创建两个管道。 There are four actions can be used in the Azure IoT Edge tasks.
   - **Azure IoT Edge - Build Module images** takes your IoT Edge solution code and builds the container images.
   - **Azure IoT Edge - Push Module images** pushes module images to the container registry you specified.
   - **Azure IoT Edge - Generate Deployment Manifest** takes a deployment.template.json file and the variables, then generates the final IoT Edge deployment manifest file.
   - **Azure IoT Edge - Deploy to IoT Edge devices** helps create IoT Edge deployments to single/multiple IoT Edge devices.

## <a name="prerequisites"></a>必备组件

* Azure Repos 存储库。 如果没有存储库，可[在项目中创建一个新的 Git 存储库](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav)。
* 提交 IoT Edge 解决方案并将其推送到存储库。 如果要为测试本文创建新的示例解决方案，请按照[在 Visual Studio Code 中开发和调试模块](how-to-vs-code-develop-module.md)或[在 Visual Studio 中开发和调试 C# 模块](how-to-visual-studio-develop-csharp-module.md)中的步骤进行操作。
   * 对于本文，你只需要 Visual Studio Code 或 Visual Studio 中的 IoT Edge 模板创建的解决方案文件夹。 在继续操作之前，无需生成、推送、部署或调试此代码。 你将在 Azure Pipelines 中设置这些进程。 
   * 如果要创建新解决方案，请首先在本地克隆存储库。 然后，在创建解决方案时，可以选择直接在存储库文件夹中创建它。 可以轻松从中提交和推送新文件。 
* 容器注册表，你可以在其中推送模块图像。 可使用 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或第三方注册表。 
* 具有至少 IoT Edge 设备的活动 [IoT 集线器](../iot-hub/iot-hub-create-through-portal.md)，用于测试单独的测试和生产部署阶段。 可按照快速入门文章在 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 上创建 IoT Edge 设备


要详细了解如何使用 Azure 存储库，请参阅 [Share your code with Visual Studio and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)（与 Visual Studio 和 Azure 存储库共享代码）

## <a name="configure-continuous-integration"></a>配置持续集成
在本部分中，将创建新的生成管道。 管道配置，使其在用户向 IoT Edge 解决方案签入任何更改时自动运行，并发布生成日志。

>[!NOTE]
>本文使用 Azure DevOps 可视化设计器。 在按照本部分中的步骤操作之前，请关闭新 YAML 管道创建体验的预览功能。 
>1. 在 Azure DevOps 中，选择个人资料图标，然后选择“预览功能”。
>2. 关闭“新的 YAML 管道创建体验”。 
>
>有关详细信息，请参阅[创建生成管道](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline)。

1. Sign into your Azure DevOps organization (**https:\//dev.azure.com/{your organization}/** ) and open the project that contains your IoT Edge solution repository.

   在本文中，我们创建了名为“IoTEdgeRepo”的存储库。 该存储库包含“IoTEdgeSolution”，其中包含名为“filtermodule”的模块代码。 

   ![打开 DevOps 项目](./media/how-to-ci-cd/init-project.png)

2. 导航到项目中的 Azure Pipelines。 打开“生成”选项卡，然后选择“新建管道”。 或者，如果已有生成管道，则选择“新建”按钮。 然后选择“新建生成管道”。

    ![创建新的生成管道](./media/how-to-ci-cd/add-new-build.png)

3. 按照提示创建管道。 

   1. 提供新生成管道的源信息。 选择“Azure Repos Git”作为源，然后选择 IoT Edge 解决方案代码所在的项目、存储库和分支。 然后选择“继续”。 

      ![选择管道源](./media/how-to-ci-cd/pipeline-source.png)

   2. 选择“空作业”而不是模板。 

      ![从空进程开始](./media/how-to-ci-cd/start-with-empty.png)

4. 创建管道后，将转到管道编辑器。 在管道描述中，根据目标平台选择正确的代理池： 
    
   * 若要在用于 Linux 容器的 amd64 平台中生成模块，请选择“托管 Ubuntu 1604”

   * 如果想在平台 amd64 中为 Windows 1809 容器生成模块，则需要[在 Windows 上设置自托管代理](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts)。

   * If you would like to build your modules in platform arm32v7 or arm64 for Linux containers, you need to [set up self-hosted agent on Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![配置生成代理池](./media/how-to-ci-cd/configure-env.png)

5. 管道预先配置了名为“代理作业 1”的作业。 Select the plus sign ( **+** ) to add three tasks to the job: **Azure IoT Edge** twice, **Copy Files** once and **Publish Build Artifacts** once. （将鼠标悬停在每个任务的名称上，以查看“添加”按钮。）

   ![添加 Azure IoT Edge 任务](./media/how-to-ci-cd/add-iot-edge-task.png)

   When all four tasks are added, your Agent job looks like the following example:
    
   ![生成管道中的三个任务](./media/how-to-ci-cd/add-tasks.png)

6. 选择第一个“Azure IoT Edge”任务进行编辑。 This task builds all modules in the solution with the target platform that you specify.

   * **Display name**: Accept the default **Azure IoT Edge - Build module images**.
   * **Action**: Accept the default **Build module images**. 
   * **.template.json file**: Select the ellipsis ( **...** ) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. 
   * **Default platform**: Select the appropriate platform for your modules based on your target IoT Edge device. 
   * **Output variables**: The output variables include a reference name that you can use to configure the file path where your deployment.json file will be generated. 将引用名称设置为令人难忘的内容，如“边缘”。 

7. 选择第二个“Azure IoT Edge”任务进行编辑。 此任务会把所有模块图像推送到所选容器注册表。

   * **Display name**: The display name is automatically updated when the action field changes. 
   * **Action**: Use the dropdown list to select **Push module images**. 
   * **Container registry type**: Select the type of container registry that you use to store your module images. 窗体会根据选择的注册表类型进行更改。 如果选择“Azure 容器注册表”，请使用下拉列表选择 Azure 订阅和容器注册表的名称。 如果选择“通用容器注册表”，请选择“新建”以创建注册表服务连接。 
   * **.template.json file**: Select the ellipsis ( **...** ) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. 
   * **Default platform**: Select the same platform as your built module images.

   如果有多个用于托管模块映像的容器注册表，则需重复此任务，选择其他容器注册表，并使用高级设置中的“绕过模块”来绕过不适用于此特定注册表的映像。

8. Select the **Copy Files** task to edit it. Use this task to copy files to the artifact staging directory.

   * **Display name**: Copy Files to: Drop folder.
   * **Contents**: Put two lines in this section, `deployment.template.json` and `**/module.json`. These two types of files are the inputs to generate IoT Edge deployment manifest. Need to be copied to the artifact staging folder and published for release pipeline.
   * **Target Folder**: Put the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description.

9. 选择“发布生成项目”任务进行编辑。 Provide artifact staging directory path to the task so that the path can be published to release pipeline.
   
   * **Display name**: Publish Artifact: drop.
   * **Path to publish**: Put the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description.
   * **Artifact name**: drop.
   * **Artifact publish location**: Azure Pipelines.


10. 打开“触发器”标签，然后选中“启用持续集成”框。 确保包含代码的分支已包括在内。

    ![开启“持续集成”触发器](./media/how-to-ci-cd/configure-trigger.png)

11. 使用“保存”按钮保存新的生成管道。

此管道现配置为在将新代码推送到存储库时自动运行。 发布管道项目的最后一项任务是触发发布管道。 继续下一部分以生成发布管道。 

## <a name="configure-continuous-deployment"></a>配置持续部署
在本部分中，会创建一个发布管道，此管道配置为在生成管道放置项目时自动运行，并且它将在 Azure Pipelines 中显示部署日志。

Create a new pipeline, and add a new stage 

1. 在“发布”选项卡上，选择“+ 新键管道”。 或者，如果已有发布管道，则选择“+ 新建”按钮并选择“+ 新建发布管道”。  

    ![添加发布管道](./media/how-to-ci-cd/add-release-pipeline.png)

2. 提示选择模板时，选择以“空作业”开头。

    ![从空作业开始](./media/how-to-ci-cd/start-with-empty-job.png)

3. 新版本管道初始化为一个阶段，称为“阶段 1”。 Rename Stage 1 to **dev** and treat it as a test environment. Usually, continuous deployment pipelines have multiple stages including **dev**, **staging** and **prod**. You can create more based on your DevOps practice. 重命名后关闭阶段详细信息窗口。 

4. 将发布链接到生成管道发布的生成项目。 在项目区域中，单击“添加”。

   ![添加项目](./media/how-to-ci-cd/add-artifacts.png)  
    
5. 在“添加项目”页中，选择源类型“生成”。 然后选择项目和创建的生成管道。 然后选择“添加”。

   ![添加生成项目](./media/how-to-ci-cd/add-an-artifact.png)

6. 打开项目触发器并选择切换以启用持续部署触发器。 现在，每次有新的生成时，都会创建新版本。

   ![配置“持续部署”触发器](./media/how-to-ci-cd/add-a-trigger.png)

7. The **dev** stage is preconfigured with one job and zero tasks. From the pipeline menu, select **Tasks** then choose the **dev** stage.  选择作业和任务计数以在此阶段配置任务。

    ![Configure dev tasks](./media/how-to-ci-cd/view-stage-tasks.png)

8. In the **dev** stage, you should see a default **Agent job**. 可配置有关代理作业的详细信息，但部署任务对平台不敏感，因此可在“代理池”（或你自己管理的任何其他代理）中使用“托管 VS2017”或“托管 Ubuntu 1604”。 

9. Select the plus sign ( **+** ) to add two task. Search for and add **Azure IoT Edge** twice.

    ![Add tasks for dev](./media/how-to-ci-cd/add-task-qa.png)

10. Select the first **Azure IoT Edge** task and configure it with the following values:

    * **Display name**: The display name is automatically updated when the action field changes. 
    * **Action**: Use the dropdown list to select **Generate deployment manifest**. 更改操作值还会更新要匹配的任务显示名称。
    * **.template.json file**: Put the path `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. The path is published from build pipeline.
    * **Default platform**: Choose the same value when building the module images.
    * **Output path**: Put the path `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. This path is the final IoT Edge deployment manifest file.

    These configurations helps replace the module image URLs in the `deployment.template.json` file. The **Generate deployment manifest** also helps replace the variables with the exact value you defined in the `deployment.template.json` file. In VS/VS Code, you are specifying the actual value in a `.env` file. In Azure Pipelines, you set the value in Release Pipeline Variables tab. Move to Variables tab and configure the Name and Value as following.

    * **ACR_ADDRESS**: Your Azure Container Registry address. 
    * **ACR_PASSWORD**: Your Azure Container Registry password.
    * **ACR_USER**: Your Azure Container Registry username.

    If you have other variables in your project, you can specify the name and value in this tab. The **Generate deployment manifest** can only recognize the variables are in `${VARIABLE}` flavor, make sure you are using this in your `*.template.json` files.

    ![Configure variables for release pipeline](./media/how-to-ci-cd/configure-variables.png)

10. Select the second **Azure IoT Edge** task and configure it with the following values:

    * **Display name**: The display name is automatically updated when the action field changes. 
    * **Action**: Use the dropdown list to select **Deploy to IoT Edge devices**. 更改操作值还会更新要匹配的任务显示名称。
    * **Azure subscription**: Select the subscription that contains your IoT Hub.
    * **IoT Hub name**: Select your IoT hub. 
    * **Choose single/multiple device**: Choose whether you want the release pipeline to deploy to one device or multiple devices. 
      * 如果部署到单个设备，请输入“IoT Edge设备 ID”。 
      * 如果要部署到多个设备，请指定设备“目标条件”。 The target condition is a filter to match a set of IoT Edge devices in IoT Hub. 若想将设备标记用作条件，则需要使用 IoT 中心设备孪生更新对应的设备标记。 在高级设置中更新“IoT Edge 部署 ID”和“IoT Edge 部署优先级”。 有关为多个设备创建部署的详细信息，请参阅[了解 IoT Edge 自动部署](module-deployment-monitoring.md)。
    * Expand Advanced Settings, select **IoT Edge deployment ID**, put the variable `$(System.TeamProject)-$(Release.EnvironmentName)`. This maps the project and release name with your IoT Edge deployment ID.

11. 选择“保存”，将更改保存到新发布管道。 从菜单中选择“管道”，返回管道视图。 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用生成和发布管道验证 IoT Edge CI/CD

要触发生成作业，可以将提交推送到源代码存储库或手动触发该作业。 在本部分中，手动触发 CI/CD 管道以测试它是否正常工作。 然后验证部署是否成功。

1. 导航到在本文开头创建的生成管道。 

2. 通过选择“队列”按钮，可在生成管道中触发生成作业（如以下屏幕截图所示）。

    ![手动触发器](./media/how-to-ci-cd/manual-trigger.png)

3. 选择生成作业以查看其进度。 If the build pipeline is completed successfully, it triggers a release to **dev** stage. 

    ![生成日志](./media/how-to-ci-cd/build-logs.png)

4. The successful **dev** release creates IoT Edge deployment to target IoT Edge devices.

    ![Release to dev](./media/how-to-ci-cd/pending-approval.png)

5. Click **dev** stage to see release logs.

    ![Release logs](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>后续步骤
* IoT Edge DevOps best practices sample in [Azure DevOps Project for IoT Edge](how-to-devops-project.md)
* 在[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)中了解 IoT Edge 部署
* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，详细了解创建、更新或删除部署的步骤。
