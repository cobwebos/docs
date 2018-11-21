---
title: Azure IoT Edge 持续集成和持续部署 | Microsoft Docs
description: Azure IoT Edge 持续集成和持续部署的概述
author: shizn
manager: ''
ms.author: xshi
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 06dec64a55aaece4cd67ebf0485e34aa206a8936
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633727"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>向 Azure IoT Edge 进行持续集成和持续部署

借助[适用于 Azure Pipelines 的 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) 和[适用于 Jenkins 的 Azure IoT Edge 插件](https://plugins.jenkins.io/azure-iot-edge)，可以轻松在 Azure IoT Edge 应用程序中采用 DevOps。 本文演示如何使用 Azure Pipelines 和 Microsoft Team Foundation Server (TFS) 的持续集成和持续部署功能，快速高效地生成和测试应用程序并将其部署到 Azure IoT Edge。 

本文介绍如何执行以下操作：
* 创建并签入示例 IoT Edge 解决方案。
* 安装适用于 Azure DevOps 的 Azure IoT Edge 扩展。
* 配置持续集成 (CI) 以生成解决方案。
* 配置持续部署 (CD) 以部署解决方案并查看响应。

完成本文中的步骤需要 30 分钟。

![CI 和 CD](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>使用 Visual Studio Code 创建示例 Azure IoT Edge 解决方案

在本部分中，创建一个示例 IoT Edge 解决方案，其中包含可在生成过程中执行的单元测试。 在按照本部分中的指导进行操作之前，请先完成[使用 Visual Studio Code 中的多个模块开发 IoT Edge 解决方案](tutorial-multiple-modules-in-vscode.md)中的步骤。

1. 在 VS Code 命令面板中，键入并运行“Azure IoT Edge: New IoT Edge Solution”命令。 然后，选择你的工作区文件夹，提供解决方案名称（默认名称为 EdgeSolution），并创建一个 C# 模块 (FilterModule) 作为此解决方案中的第一个用户模块。 还需要为你的第一个模块指定 Docker 映像存储库。 默认映像存储库基于本地 Docker 注册表 (`localhost:5000/filtermodule`)。 为了进一步的持续集成，需要将其更改为 Azure 容器注册表 (`<your container registry address>/filtermodule`) 或 Docker 中心。

    ![设置 ACR](./media/how-to-ci-cd/acr.png)

2. VS Code 窗口将加载你的 IoT Edge 解决方案空间。 可选择键入并运行“Azure IoT Edge: Add IoT Edge module”以添加更多模块。 根文件夹中有一个 `modules` 文件夹、一个 `.vscode` 文件夹和一个部署清单模板文件。 所有用户模块代码都将成为文件夹 `modules` 下的子文件夹。 `deployment.template.json` 是部署清单模板。 此文件中的某些参数将通过 `module.json` 进行分析，它存在于每个模块文件夹中。

3. 现在，示例 IoT Edge 解决方案已准备就绪。 默认 C# 模块充当管道消息模块。 在 `deployment.template.json` 中，你将看到此解决方案包含两个模块。 消息将从 `tempSensor` 模块生成，并且将通过 `FilterModule` 直接输送，然后发送到 IoT 中心。

4. 保存这些项目，然后将其签入 Azure Repos 或 TFS 存储库。
    
> [!NOTE]
> 若要详细了解如何使用 Azure 存储库，请参阅 [Share your code with Visual Studio and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)（与 Visual Studio 和 Azure 存储库共享代码）。


## <a name="configure-azure-pipelines-for-continuous-integration"></a>配置 Azure Pipelines，以进行持续集成
在本部分中，将创建一个生成管道，此管道配置为在将任何更改签入示例 IoT Edge 解决方案时自动运行，并且它将在 Azure Pipelines 中显示生成日志。

1. 登录 Azure DevOps 组织 (https://dev.azure.com/{your organization}/) 并打开签入了示例应用的项目。

    ![签入代码](./media/how-to-ci-cd/init-project.png)

1. 请访问 Azure DevOps 市场中的[适用于 Azure Pipelines 的 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy)。 单击“免费获取”并按照向导操作，将此扩展安装至 Azure DevOps 组织或下载到 TFS。

    ![安装扩展](./media/how-to-ci-cd/install-extension.png)

1. 在 Azure Pipelines 中打开“生成和发布”中心，并在“生成”选项卡中选择“+ 新建管道”。 或者，如果已有生成管道，则选择“+ 新建”按钮。

    ![新建管道](./media/how-to-ci-cd/add-new-build.png)

1. 若出现提示，请选择“Git”源类型。 然后选择代码所在的项目、存储库和分支。 选择“继续”。

    ![选择 git](./media/how-to-ci-cd/select-vsts-git.png)

    在“选择模板”窗口中选择“从空进程开始”。

    ![选择模板](./media/how-to-ci-cd/start-with-empty.png)

1. 在管道编辑器中，选择代理池。 
    
    * 若要在用于 Linux 容器的 amd64 平台中生成模块，请选择“托管 Ubuntu 1604”
    * 若要在用于 Windows 容器的 amd64 平台中生成模块，请选择“托管 VS2017” 
    * 若要在用于 Linux 容器的 arm32v7 平台中生成模块，则需要通过单击“管理”按钮来设置自己的生成代理。
    
    ![配置生成代理](./media/how-to-ci-cd/configure-env.png)

1. 在代理作业中单击“+”，以在生成管道中添加两个任务。 第一个任务来自“Azure IoT Edge”。 第二个任务来自“发布生成项目”
    
    ![添加任务](./media/how-to-ci-cd/add-tasks.png)

1. 在第一个 Azure IoT Edge 任务中，将“显示名称”更新为“模块生成和推送”，并在“操作”下拉列表中选择“生成并推送”。 在“Module.json 文件”文本框中添加下面的路径。 然后选择“容器注册表类型”，确保在代码中配置并选择相同的注册表 (module.json)。 此任务将生成并推送解决方案中的所有模块，并发布至指定的容器注册表。 如果要将模块推送至不同的注册表，则会有多个“模块生成和推送”任务。 如果 IoT Edge 解决方案不在代码存储库的根目录下，则可以在生成定义中指定 Edge 解决方案根路径。
    
    ![生成并推送](./media/how-to-ci-cd/build-and-push.png)

1. 在“发布生成项目”任务中，将指定生成任务生成的部署文件。 将“要发布的路径”设置为“config/deployment.json”。 如果在上一个任务中设置的是“Edge 解决方案根路径”，则必须在此处加入根路径。 例如，如果 Edge 解决方案根路径为“./edgesolution”，则“要发布的路径”应为“./edgesolution/config/deployment.json”。 `deployment.json` 文件是在生成期间生成的，因此可以放心忽略文本框中的红色错误行。 

    ![发布项目](./media/how-to-ci-cd/publish-build-artifacts.png)

1. 打开“触发器”选项卡并开启“持续集成”触发器。 确保包含代码的分支已包括在内。

    ![配置触发器](./media/how-to-ci-cd/configure-trigger.png)

    保存新的生成管道。 单击“保存”按钮  。


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>配置 Azure Pipelines，以进行持续部署
在本部分中，将创建一个发布管道，此管道配置为在生成管道放置项目时自动运行，并且它将在 Azure Pipelines 中显示部署日志。

1. 在“发布”选项卡上，选择“+ 新键管道”。 或者，如果已有发布管道，则选择“+ 新建”按钮。  

    ![添加发布管道](./media/how-to-ci-cd/add-release-pipeline.png)

    在“选择模板”窗口中，选择“从空作业开始”。

    ![从空作业开始](./media/how-to-ci-cd/start-with-empty-job.png)

2. 然后发布管道将初始化一个阶段：阶段 1。 将“阶段 1”重命名为“QA”，并将其视为测试环境。 在典型的持续部署管道中，它通常存在多个阶段，你可以根据 DevOps 实践创建更多阶段。

    ![创建阶段](./media/how-to-ci-cd/QA-env.png)

3. 将发布链接到生成项目。 在项目区域中，单击“添加”。

    ![添加项目](./media/how-to-ci-cd/add-artifacts.png)  
    
    在“添加项目”页中，选择源类型“生成”。 然后选择项目和创建的生成管道。 然后单击“添加”。

    ![添加项目](./media/how-to-ci-cd/add-an-artifact.png)

    打开持续部署触发器，以便在每次新的生成可用时创建新发布。

    ![配置触发器](./media/how-to-ci-cd/add-a-trigger.png)

4. 导航到 QA 阶段，并配置处于此阶段的任务。

    ![配置 QA 任务](./media/how-to-ci-cd/view-stage-tasks.png)

   部署任务对平台不敏感，这意味着可以在代理池（或你自己管理的任何其他代理）中选择“托管 VS2017”或“托管 Ubuntu 1604 ”。 单击“+”，然后添加一个任务。

    ![添加 QA 任务](./media/how-to-ci-cd/add-task-qa.png)

5. 在 Azure IoT Edge 任务中，导航到“操作”下拉列表，选择“部署到 IoT Edge 设备”。 选择 Azure 订阅并输入你的 IoT 中心名称。 可以指定 IoT Edge 部署 ID 和部署优先级。 还可以选择部署至单个设备或多个设备。 如果要部署到多个设备，需要指定设备目标条件。 目标条件是用于在 IoT 中心匹配一组 Edge 设备的筛选器。 若想将设备标记用作条件，则需要使用 IoT 中心设备孪生更新对应的设备标记。 假设已将多个 IoT Edge 设备标记为“qa”，则任务配置应如以下屏幕截图中所示。 

    ![部署到 QA](./media/how-to-ci-cd/deploy-to-qa.png)

    保存新发布管道。 单击“保存”按钮  。 然后，单击“管道”以返回到管道。

6. 第二个阶段针对生产环境。 若要添加新阶段“PROD”，只需克隆“QA”阶段并将克隆的阶段重命名为“PROD”即可。

    ![克隆阶段](./media/how-to-ci-cd/clone-stage.png)

7. 为生产环境配置任务。 假设已将多个 IoT Edge 设备标记为“prod”，请在任务配置中，将目标条件更新为“prod”，并将部署 ID 设置为“deploy-prod”。 单击“保存”按钮  。 然后，单击“管道”以返回到管道。
    
    ![部署到生产环境](./media/how-to-ci-cd/deploy-to-prod.png)

7. 目前，在 QA 阶段和 PROD 阶段将会连续触发生成项目。 但大多数情况下，需要在 QA 设备上集成一些测试用例并手动审批位。 之后会将这些位部署到 PROD 环境。 在 PROD 阶段按照如下所示设置审批。

    1. 打开“预先部署条件”设置面板。

        ![打开预先部署条件](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. 在“预先部署审批”中，设置“已启用”。 并填写“审批者”输入栏。 然后单击“保存”。
    
        ![设置条件](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. 现在，发布管道已按照以下所示进行设置。

    ![发布管道](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用生成和发布管道验证 IoT Edge CI/CD

在本部分中，将触发生成作业，以使 CI/CD 管道正常工作。 然后验证部署是否成功。

1. 要触发生成作业，可以将提交推送到源代码存储库或手动触发该作业。 通过单击“队列”按钮，可在生成管道中触发生成作业（如以下屏幕截图所示）。

    ![手动触发器](./media/how-to-ci-cd/manual-trigger.png)

2. 如果生成管道成功完成，它将触发到 QA 阶段的发布。 导航到生成管道日志，应该会看到以下内容。

    ![生成日志](./media/how-to-ci-cd/build-logs.png)

3. 成功部署到 QA 阶段将会触发通知（发送给审批者）。 导航到发布管道，可以看到以下内容。 

    ![待审批](./media/how-to-ci-cd/pending-approval.png)


4. 审批者批准此更改后，便可将其部署到 PROD。

    ![部署到 prod](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>后续步骤

* 在[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)中了解 IoT Edge 部署
* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，详细了解创建、更新或删除部署的步骤。
