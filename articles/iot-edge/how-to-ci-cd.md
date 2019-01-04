---
title: 持续集成和持续部署 - Azure IoT Edge | Microsoft Docs
description: 设置持续集成和持续部署 - Azure IoT Edge 以及 Azure DevOps、Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a714cec5ce05473887f9f06d47c75563bf878081
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386819"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>向 Azure IoT Edge 进行持续集成和持续部署

借助 Azure Pipelines 中的内置 Azure IoT Edge 任务或 Jenkins 服务器上[适用于 Jenkins 的 Azure IoT Edge 插件](https://plugins.jenkins.io/azure-iot-edge)，可以轻松地在 Azure IoT Edge 应用程序中采用 DevOps。 本文演示了如何使用 Azure Pipelines 的持续集成和持续部署功能，快速高效地生成和测试应用程序并将其部署到 Azure IoT Edge。 

本文介绍如何执行以下操作：
* 创建并签入示例 IoT Edge 解决方案。
* 配置持续集成 (CI) 以生成解决方案。
* 配置持续部署 (CD) 以部署解决方案并查看响应。

![示意图 - 用于开发和生产的 CI 和 CD 分支](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>使用 Visual Studio Code 创建示例 Azure IoT Edge 解决方案

在本部分中，创建一个示例 IoT Edge 解决方案，其中包含可在生成过程中执行的单元测试。 在按照本部分中的指导进行操作之前，请先完成[使用 Visual Studio Code 中的多个模块开发 IoT Edge 解决方案](how-to-develop-multiple-modules-vscode.md)中的步骤。

1. 在 VS Code 命令面板中，键入并运行 **Azure IoT Edge:New IoT Edge solution** 命令。 然后，选择你的工作区文件夹，提供解决方案名称（默认名称为 EdgeSolution），并创建一个 C# 模块 (FilterModule) 作为此解决方案中的第一个用户模块。 还需要为你的第一个模块指定 Docker 映像存储库。 默认映像存储库基于本地 Docker 注册表 (`localhost:5000/filtermodule`)。 为了进一步的持续集成，请将其更改为 Azure 容器注册表 (`<your container registry address>/filtermodule`) 或 Docker 中心。

    ![设置 Azure 容器注册表](./media/how-to-ci-cd/acr.png)

2. VS Code 窗口将加载你的 IoT Edge 解决方案空间。 还可以键入并运行 **Azure IoT Edge:Add IoT Edge module** 来添加更多模块。 根文件夹中有一个 `modules` 文件夹、一个 `.vscode` 文件夹和一个部署清单模板文件。 所有用户模块代码都将成为文件夹 `modules` 下的子文件夹。 `deployment.template.json` 是部署清单模板。 此文件中的某些参数将通过 `module.json` 进行分析，它存在于每个模块文件夹中。

3. 现在，示例 IoT Edge 解决方案已准备就绪。 默认 C# 模块充当管道消息模块。 在 `deployment.template.json` 中，你将看到此解决方案包含两个模块。 消息将从 `tempSensor` 模块生成，并且将通过 `FilterModule` 直接输送，然后发送到 IoT 中心。

4. 保存这些项目，然后将其提交到 Azure Repos。
    
> [!NOTE]
> 若要详细了解如何使用 Azure 存储库，请参阅 [Share your code with Visual Studio and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)（与 Visual Studio 和 Azure 存储库共享代码）。


## <a name="configure-azure-pipelines-for-continuous-integration"></a>配置 Azure Pipelines，以进行持续集成
在本部分中，将创建一个生成管道，此管道配置为在将任何更改签入示例 IoT Edge 解决方案时自动运行，并且它将在 Azure Pipelines 中显示生成日志。

1. 登录 Azure DevOps 组织 (https://dev.azure.com/{your organization}/) 并打开签入了示例应用的项目。

    ![将代码签入到 Azure Pipelines](./media/how-to-ci-cd/init-project.png)

1. 在 Azure Pipelines 中打开“生成”选项卡，选择“+ 新建管道”。 或者，如果已有生成管道，则选择“+ 新建”按钮。 然后选择“新建生成管道”。

    ![创建新的生成管道](./media/how-to-ci-cd/add-new-build.png)

1. 在出现提示时，选择源的 Azure Repos。 然后选择代码所在的项目、存储库和分支。 选择“继续”。

    ![选择 Azure Repos Git](./media/how-to-ci-cd/select-vsts-git.png)

    在“选择模板”窗口中选择“从空进程开始”。

    ![从空进程开始](./media/how-to-ci-cd/start-with-empty.png)

1. 在管道编辑器中，选择代理池。 
    
    * 若要在用于 Linux 容器的 amd64 平台中生成模块，请选择“托管 Ubuntu 1604”
    * 若要在用于 Windows 容器的 amd64 平台中生成模块，请选择“托管 VS2017” 
    * 若要在用于 Linux 容器的 arm32v7 平台中生成模块，则需要通过单击“管理”按钮来设置自己的生成代理。
    
    ![配置生成代理池](./media/how-to-ci-cd/configure-env.png)

1. 在代理作业中单击“+”，以在生成管道中添加三个任务。 头两个任务来自“Azure IoT Edge”。 第三个任务来自“发布生成项目”
    
    ![将任务添加到生成管道](./media/how-to-ci-cd/add-tasks.png)

1. 在第一个 Azure IoT Edge 任务中，将“显示名称”更新为“Azure IoT Edge - 生成模块映像”，并在“操作”下拉列表中选择“生成模块映像”。 在 **.template.json 文件**控件中，选择 **deployment.template.json** 文件，该文件描述 IoT Edge 解决方案。 然后选择“默认平台”，确保选择与 IoT Edge 设备相同的平台。 此任务会通过指定的目标平台生成解决方案中的所有模块。 另外还会生成 **deployment.json** 文件，可以在“输出变量”中找到文件路径。 对于此变量，请将别名设置为 `edge`。
    
    ![配置“生成模块映像”任务](./media/how-to-ci-cd/build-and-push.png)

1. 在第二个 Azure IoT Edge 任务中，将“显示名称”更新为“Azure IoT Edge - 推送模块映像”，并在“操作”下拉列表中选择“推送模块映像”。 选择“容器注册表类型”，确保在代码中配置并选择相同的注册表 (module.json)。 在 **.template.json 文件**控件中，选择 **deployment.template.json** 文件，该文件描述 IoT Edge 解决方案。 然后选择“默认平台”，确保为已生成的模块映像选择相同的平台。 此任务会将所有模块映像推送到所选容器注册表。 另外还会在 **deployment.json** 文件中添加容器注册表凭据，可以在“输出变量”中找到文件路径。 对于此变量，请将别名设置为 `edge`。 如果有多个用于托管模块映像的容器注册表，则需重复此任务，选择其他容器注册表，并使用高级设置中的“绕过模块”来绕过不适用于此特定注册表的映像。

    ![配置“推送模块映像”任务](./media/how-to-ci-cd/push.png)

1. 在“发布生成项目”任务中，将指定生成任务生成的部署文件。 将“要发布的路径”设置为 `$(edge.DEPLOYMENT_FILE_PATH)`。

    ![配置“发布项目”任务](./media/how-to-ci-cd/publish-build-artifacts.png)

1. 打开“触发器”选项卡并开启“持续集成”触发器。 确保包含代码的分支已包括在内。

    ![开启“持续集成”触发器](./media/how-to-ci-cd/configure-trigger.png)

    保存新的生成管道。 单击“保存”按钮  。


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>配置 Azure Pipelines，以进行持续部署
在本部分中，将创建一个发布管道，此管道配置为在生成管道放置项目时自动运行，并且它将在 Azure Pipelines 中显示部署日志。

1. 在“发布”选项卡上，选择“+ 新建管道”。 或者，如果已有发布管道，则选择“+ 新建”按钮并单击“+ 新建发布管道”。  

    ![添加发布管道](./media/how-to-ci-cd/add-release-pipeline.png)

    在“选择模板”窗口中，选择“从空作业开始”。

    ![从空作业开始](./media/how-to-ci-cd/start-with-empty-job.png)

2. 然后发布管道将初始化一个阶段：**阶段 1**。 将“阶段 1”重命名为“QA”，并将其视为测试环境。 在典型的持续部署管道中，它通常存在多个阶段，你可以根据 DevOps 实践创建更多阶段。

    ![“创建测试环境”阶段](./media/how-to-ci-cd/QA-env.png)

3. 将发布链接到生成项目。 在项目区域中，单击“添加”。

    ![添加项目](./media/how-to-ci-cd/add-artifacts.png)  
    
    在“添加项目”页中，选择源类型“生成”。 然后选择项目和创建的生成管道。 然后单击“添加”。

    ![添加生成项目](./media/how-to-ci-cd/add-an-artifact.png)

    打开持续部署触发器，以便在每次新的生成可用时创建新发布。

    ![配置“持续部署”触发器](./media/how-to-ci-cd/add-a-trigger.png)

4. 导航到 QA 阶段，并配置处于此阶段的任务。

    ![配置 QA 任务](./media/how-to-ci-cd/view-stage-tasks.png)

   部署任务对平台不敏感，这意味着可以在代理池（或你自己管理的任何其他代理）中选择“托管 VS2017”或“托管 Ubuntu 1604 ”。 单击“+”，然后添加一个任务。

    ![添加 QA 任务](./media/how-to-ci-cd/add-task-qa.png)

5. 在 Azure IoT Edge 任务中，导航到“操作”下拉列表，选择“部署到 IoT Edge 设备”。 选择 Azure 订阅并输入你的 IoT 中心名称。 可以选择部署至单个设备或多个设备。 如果要部署到多个设备，需要指定设备目标条件。 目标条件是用于在 IoT 中心匹配一组 Edge 设备的筛选器。 若想将设备标记用作条件，则需要使用 IoT 中心设备孪生更新对应的设备标记。 在高级设置中将“IoT Edge 部署 ID”更新为“deploy-qa”。 假设已将多个 IoT Edge 设备标记为“qa”，则任务配置应如以下屏幕截图中所示。 

    ![部署到 QA](./media/how-to-ci-cd/deploy-to-qa.png)

    保存新发布管道。 单击“保存”按钮  。 然后，单击“管道”以返回到管道。

6. 第二个阶段针对生产环境。 若要添加新阶段“PROD”，可以克隆“QA”阶段并将克隆的阶段重命名为“PROD”。

    ![克隆阶段](./media/how-to-ci-cd/clone-stage.png)

7. 为生产环境配置任务。 假设已将多个 IoT Edge 设备标记为“prod”，请在任务配置中将目标条件更新为“prod”，并在高级设置中将部署 ID 设置为“deploy-prod”。 单击“保存”按钮  。 然后，单击“管道”以返回到管道。
    
    ![部署到生产环境](./media/how-to-ci-cd/deploy-to-prod.png)

7. 目前，在 QA 阶段和 PROD 阶段将会连续触发生成项目。 但大多数情况下，需要在 QA 设备上集成一些测试用例并手动审批位。 之后会将这些位部署到 PROD 环境。 在 PROD 阶段按照以下屏幕截图所示来设置审批。

    1. 打开“预先部署条件”设置面板。

        ![打开预先部署条件](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. 在“预先部署审批”中，设置“已启用”。 并填写“审批者”输入栏。 然后单击“保存”。
    
        ![设置条件](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. 现在，发布管道已按照以下屏幕截图所示进行设置。

    ![发布管道](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用生成和发布管道验证 IoT Edge CI/CD

在本部分中，将触发生成作业，以使 CI/CD 管道正常工作。 然后验证部署是否成功。

1. 要触发生成作业，可以将提交推送到源代码存储库或手动触发该作业。 通过单击“队列”按钮，可在生成管道中触发生成作业（如以下屏幕截图所示）。

    ![手动触发器](./media/how-to-ci-cd/manual-trigger.png)

2. 如果生成管道成功完成，它将触发到 QA 阶段的发布。 导航到生成管道日志，应该会看到以下屏幕截图。

    ![生成日志](./media/how-to-ci-cd/build-logs.png)

3. 成功部署到 QA 阶段将会触发通知（发送给审批者）。 导航到发布管道，可以看到以下屏幕截图。 

    ![待审批](./media/how-to-ci-cd/pending-approval.png)


4. 审批者批准此更改后，便可将其部署到 PROD。

    ![部署到 prod](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>后续步骤

* 在[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)中了解 IoT Edge 部署
* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，详细了解创建、更新或删除部署的步骤。
