---
title: 持续集成和持续部署 - Azure IoT Edge | Microsoft Docs
description: 设置持续集成和持续部署 - Azure IoT Edge 以及 Azure DevOps、Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 659a6f5acaac848084ed1e9590a414191542b54a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414629"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>向 Azure IoT Edge 进行持续集成和持续部署

借助 Azure Pipelines 中的内置 Azure IoT Edge 任务，可以轻松地在 Azure IoT Edge 应用程序中采用 DevOps。 本文演示了如何使用 Azure Pipelines 的持续集成和持续部署功能，快速高效地生成和测试应用程序并将其部署到 Azure IoT Edge。 

在本文中，你将学习如何使用 Azure Pipelines 的内置 Azure IoT Edge 任务为 IoT Edge 解决方案创建两个管道。 第一个获取代码并生成解决方案，将模块图像推送到容器注册表并创建部署清单。 第二个将模块部署到目标 IoT Edge 设备。  

![示意图 - 用于开发和生产的 CI 和 CD 分支](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>系统必备

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
>有关详细信息，请参阅[创建生成管道](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline)。

1. 登录 Azure DevOps 组织 (**https:\//dev.azure.com/{your organization}/** ) 并打开包含 IoT Edge 解决方案存储库的项目。

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

   * 如果要在平台 arm32v7 或 arm64 for Linux 容器中生成模块, 需在[linux 上设置自托管代理](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)。
    
     ![配置生成代理池](./media/how-to-ci-cd/configure-env.png)

5. 管道预先配置了名为“代理作业 1”的作业。 选择加号 (+)，向作业添加三个任务：“Azure IoT Edge”两次，“发布生成项目”一次。 （将鼠标悬停在每个任务的名称上，以查看“添加”按钮。）

   ![添加 Azure IoT Edge 任务](./media/how-to-ci-cd/add-iot-edge-task.png)

   添加所有三个任务后，代理作业如下所示：
    
   ![生成管道中的三个任务](./media/how-to-ci-cd/add-tasks.png)

6. 选择第一个“Azure IoT Edge”任务进行编辑。 此任务使用指定的目标平台生成解决方案中的所有模块，它还会生成“deployment.json”文件，该文件描述 IoT Edge 设备如何配置部署。

   * **显示名称**：接受默认的“Azure IoT Edge - 生成模块图像”。
   * **操作**：接受默认的“生成模块图像”。 
   * **.template.json 文件**：选择省略号 (...) 并导航到包含 IoT Edge 解决方案的存储库中的“deployment.template.json”文件。 
   * **默认平台**：根据目标 IoT Edge 设备为模块选择相应的平台。 
   * **输出变量**：输出变量包含引用名称，可使用该名称配置生成 deployment.json 文件的文件路径。 将引用名称设置为令人难忘的内容，如“边缘”。 

7. 选择第二个“Azure IoT Edge”任务进行编辑。 此任务会把所有模块图像推送到所选容器注册表。 它还会将容器注册表凭据添加到“deployment.json”文件中，以便 IoT Edge 设备可以访问模块图像。 

   * **显示名称**：操作字段更改时，显示名称会自动更新。 
   * **操作**：使用下拉列表选择“推送模块图像”。 
   * **容器注册表类型**：选择用于存储模块图像的容器注册表类型。 窗体会根据选择的注册表类型进行更改。 如果选择“Azure 容器注册表”，请使用下拉列表选择 Azure 订阅和容器注册表的名称。 如果选择“通用容器注册表”，请选择“新建”以创建注册表服务连接。 
   * **.template.json 文件**：选择省略号 (...) 并导航到包含 IoT Edge 解决方案的存储库中的“deployment.template.json”文件。 
   * **默认平台**：选择与生成的模块图像相同的平台。

   如果有多个用于托管模块映像的容器注册表，则需重复此任务，选择其他容器注册表，并使用高级设置中的“绕过模块”来绕过不适用于此特定注册表的映像。

8. 选择“发布生成项目”任务进行编辑。 提供生成任务生成的部署文件的文件路径。 设置“发布路径”值以匹配在生成模块任务中设置的输出变量。 例如， `$(edge.DEPLOYMENT_FILE_PATH)` 。 将其他值保留为其默认值。 

9. 打开“触发器”标签，然后选中“启用持续集成”框。 确保包含代码的分支已包括在内。

    ![开启“持续集成”触发器](./media/how-to-ci-cd/configure-trigger.png)

10. 使用“保存”按钮保存新的生成管道。

此管道现配置为在将新代码推送到存储库时自动运行。 发布管道项目的最后一项任务是触发发布管道。 继续下一部分以生成发布管道。 

## <a name="configure-continuous-deployment"></a>配置持续部署
在本部分中，会创建一个发布管道，此管道配置为在生成管道放置项目时自动运行，并且它将在 Azure Pipelines 中显示部署日志。

在本部分中，将创建两个不同的阶段，一个用于测试部署，另一个用于生产部署。 

### <a name="create-test-stage"></a>创建测试阶段

创建新管道，并为质量保证 (QA) 部署配置其第一阶段。 

1. 在“发布”选项卡上，选择“+ 新键管道”。 或者，如果已有发布管道，则选择“+ 新建”按钮并选择“+ 新建发布管道”。  

    ![添加发布管道](./media/how-to-ci-cd/add-release-pipeline.png)

2. 提示选择模板时，选择以“空作业”开头。

    ![从空作业开始](./media/how-to-ci-cd/start-with-empty-job.png)

3. 新版本管道初始化为一个阶段，称为“阶段 1”。 将阶段 1 重命名为“QA”，并将其视为测试环境。 通常情况下，持续部署管道具有多个阶段。 可以根据 DevOps 实践创建更多内容。 重命名后关闭阶段详细信息窗口。 

    ![“创建测试环境”阶段](./media/how-to-ci-cd/QA-env.png)

4. 将发布链接到生成管道发布的生成项目。 在项目区域中，单击“添加”。

   ![添加项目](./media/how-to-ci-cd/add-artifacts.png)  
    
5. 在“添加项目”页中，选择源类型“生成”。 然后选择项目和创建的生成管道。 然后选择“添加”。

   ![添加生成项目](./media/how-to-ci-cd/add-an-artifact.png)

6. 打开项目触发器并选择切换以启用持续部署触发器。 现在，每次有新的生成时，都会创建新版本。

   ![配置“持续部署”触发器](./media/how-to-ci-cd/add-a-trigger.png)

7. “QA”阶段预先配置了一个作业和零个任务。 从管道菜单中，选择“任务”，然后选择“QA”阶段。  选择作业和任务计数以在此阶段配置任务。

    ![配置 QA 任务](./media/how-to-ci-cd/view-stage-tasks.png)

8. 在 QA 阶段，你应看到默认的“代理作业”。 可配置有关代理作业的详细信息，但部署任务对平台不敏感，因此可在“代理池”（或你自己管理的任何其他代理）中使用“托管 VS2017”或“托管 Ubuntu 1604”。 

9. 选择加号 (+)，添加一个任务。 搜索并添加“Azure IoT Edge”。 

    ![添加 QA 任务](./media/how-to-ci-cd/add-task-qa.png)

10. 选择新的 Azure IoT Edge 任务，并使用以下值对其进行配置：

    * **显示名称**：操作字段更改时，显示名称会自动更新。 
    * **操作**：使用下拉列表选择“部署到 IoT Edge 设备”。 更改操作值还会更新要匹配的任务显示名称。
    * **Azure 订阅**：选择包含 IoT 中心的订阅。
    * **IoT 中心名称**：选择 IoT 中心。 
    * **选择单个/多个设备**：选择是否要将发布管道部署到一个设备或多个设备。 
      * 如果部署到单个设备，请输入“IoT Edge设备 ID”。 
      * 如果要部署到多个设备，请指定设备“目标条件”。 目标条件是用于在 IoT 中心匹配一组 Edge 设备的筛选器。 若想将设备标记用作条件，则需要使用 IoT 中心设备孪生更新对应的设备标记。 在高级设置中更新“IoT Edge 部署 ID”和“IoT Edge 部署优先级”。 有关为多个设备创建部署的详细信息，请参阅[了解 IoT Edge 自动部署](module-deployment-monitoring.md)。

11. 选择“保存”，将更改保存到新发布管道。 从菜单中选择“管道”，返回管道视图。 

### <a name="create-production-stage"></a>创建生产阶段

在生产部署的发布管道中创建第二个阶段。 

1. 通过克隆 QA 阶段创建生产的第二阶段。 将光标悬停在 QA 阶段上，然后选择克隆按钮。 

    ![克隆阶段](./media/how-to-ci-cd/clone-stage.png)

2. 选择名为“QA 副本”的新阶段，以打开其属性。 将阶段名称更改为“PROD”，用于生产。 关闭阶段属性窗口。 

3. 要打开 PROD 阶段任务，请从管道菜单中选择“任务”，然后选择“PROD”阶段。 

4. 选择 Azure IoT Edge 任务以配置是否适用于生产环境。 QA 和 PROD 的部署设置可能相同，只是要在生产中定位不同的设备或设备集。 更新设备 ID 字段，或生产设备的目标条件和部署 ID 字段。 

5. 使用“保存”按钮将其保存。 然后，选择“管道”以返回到管道视图。
    
6. 按照当前配置此发布管道的方式，每次完成新生成时，生成项目将触发“QA”阶段，然后触发“PROD”阶段。 但通常建议在 QA 设备上集成一些测试用例，并手动批准生产部署。 使用以下步骤为 PROD 阶段创建审批条件：

    1. 打开“预部署条件”设置面板。

        ![打开预先部署条件](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. 将“预部署审批”条件切换为“已启用”。 在“审批者”字段中添加一个或多个用户或组，并自定义所需的任何其他审批策略。 若要保存更改，请关闭预部署条件面板。
    
       ![设置条件](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. 使用“保存”按钮保存发布管道。 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用生成和发布管道验证 IoT Edge CI/CD

要触发生成作业，可以将提交推送到源代码存储库或手动触发该作业。 在本部分中，手动触发 CI/CD 管道以测试它是否正常工作。 然后验证部署是否成功。

1. 导航到在本文开头创建的生成管道。 

2. 通过选择“队列”按钮，可在生成管道中触发生成作业（如以下屏幕截图所示）。

    ![手动触发器](./media/how-to-ci-cd/manual-trigger.png)

3. 选择生成作业以查看其进度。 如果生成管道成功完成，则会触发到“QA”阶段的发布。 

    ![生成日志](./media/how-to-ci-cd/build-logs.png)

4. 成功部署到“QA”阶段会触发通知（发送给审批者）。 验证模块是否已在使用 QA 阶段设定的设备上成功部署。 然后，选择“PROD”按钮，再选择“批准”，导航到发布管道并批准发布进入 PROD 阶段。 

    ![待审批](./media/how-to-ci-cd/pending-approval.png)

5. 审批者批准此更改后，便可将其部署到 PROD。

## <a name="next-steps"></a>后续步骤

* 在[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)中了解 IoT Edge 部署
* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，详细了解创建、更新或删除部署的步骤。
