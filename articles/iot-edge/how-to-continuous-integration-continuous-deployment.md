---
title: 持续集成和持续部署到 Azure IoT Edge 设备-Azure IoT Edge
description: 使用 Azure IoT Edge YAML 与 Azure DevOps、Azure Pipelines 来设置持续集成和持续部署
author: shizn
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d29a5a6d0d4745655ce5b6d0cead3eaba77ed423
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281620"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>持续集成和持续部署到 Azure IoT Edge 设备

借助 Azure Pipelines 中的内置 Azure IoT Edge 任务，可以轻松地在 Azure IoT Edge 应用程序中采用 DevOps。 本文演示如何使用 YAML 的持续集成和持续部署 Azure Pipelines 功能，通过快速高效地生成、测试和部署应用程序并将其部署到 Azure IoT Edge。 或者，可以 [使用经典编辑器](how-to-continuous-integration-continuous-deployment-classic.md)。

![示意图 - 用于开发和生产的 CI 和 CD 分支](./media/how-to-continuous-integration-continuous-deployment/model.png)

本文介绍如何使用 Azure Pipelines 的内置 [Azure IoT Edge 任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/azure-iot-edge) 创建 IoT Edge 解决方案的生成和发布管道。 添加到管道中的每个 Azure IoT Edge 任务都实现以下四个操作之一：

 | 操作 | 说明 |
 | --- | --- |
 | 生成模块映像 | 获取 IoT Edge 解决方案代码并生成容器映像。|
 | 推送模块映像 | 将模块映像推送到指定的容器注册表。 |
 | 生成部署清单 | 对文件和变量使用 deployment.template.js，然后生成最终的 IoT Edge 部署清单文件。 |
 | 部署到 IoT Edge 设备 | 创建 IoT Edge 部署到一个或多个 IoT Edge 设备。 |

除非另行指定，否则本文中的过程不会浏览通过任务参数提供的所有功能。 有关详细信息，请参阅以下主题：

* [任务版本](https://docs.microsoft.com/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=classic#task-versions)
* **高级** -如果适用，请指定不想生成的模块。
* [控制选项](https://docs.microsoft.com/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=classic#task-control-options)
* [环境变量](https://docs.microsoft.com/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#environment-variables)
* [输出变量](https://docs.microsoft.com/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#use-output-variables-from-tasks)

## <a name="prerequisites"></a>先决条件

* Azure Repos 存储库。 如果没有存储库，可[在项目中创建一个新的 Git 存储库](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav)。 在本文中，我们创建了名为“IoTEdgeRepo”的存储库。
* 提交 IoT Edge 解决方案并将其推送到存储库。 如果要为测试本文创建新的示例解决方案，请按照[在 Visual Studio Code 中开发和调试模块](how-to-vs-code-develop-module.md)或[在 Visual Studio 中开发和调试 C# 模块](how-to-visual-studio-develop-csharp-module.md)中的步骤进行操作。 在本文中，我们在名为 **IoTEdgeSolution**的存储库中创建了一个解决方案，其中包含名为 **filtermodule**的模块的代码。

   对于本文，你只需要 Visual Studio Code 或 Visual Studio 中的 IoT Edge 模板创建的解决方案文件夹。 在继续操作之前，无需生成、推送、部署或调试此代码。 您将在 Azure Pipelines 中设置这些进程。

   如果要创建新解决方案，请首先在本地克隆存储库。 然后，在创建解决方案时，可以选择直接在存储库文件夹中创建它。 可以轻松从中提交和推送新文件。

* 容器注册表，你可以在其中推送模块图像。 可使用 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或第三方注册表。
* 至少具有两个 IoT Edge 设备的活动 Azure [IoT 中心](../iot-hub/iot-hub-create-through-portal.md) ，用于测试单独的测试和生产部署阶段。 可按照快速入门文章在 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 上创建 IoT Edge 设备

要详细了解如何使用 Azure 存储库，请参阅 [Share your code with Visual Studio and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)（与 Visual Studio 和 Azure 存储库共享代码）

## <a name="create-a-build-pipeline-for-continuous-integration"></a>创建持续集成的生成管道

在本部分中，将创建新的生成管道。 在签入对示例 IoT Edge 解决方案的任何更改并发布生成日志时，可以将管道配置为自动运行。

1. 登录到 Azure DevOps 组织 (`https://dev.azure.com/{your organization}`) 并打开包含 IoT Edge 解决方案存储库的项目。

   ![打开 DevOps 项目](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. 在项目的左窗格菜单中，选择 " **管道**"。 在页面中心选择 " **创建管道** "。 或者，如果已有生成管道，请选择右上方的 " **新建管道** " 按钮。

    ![使用 "新建管道" 按钮创建新的生成管道](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. 在 "**你的代码位于何处？** " 页上，选择 " **Azure Repos Git `YAML` **"。 如果要使用经典编辑器创建项目的生成管道，请参阅 [经典编辑器指南](how-to-continuous-integration-continuous-deployment-classic.md)。

4. 选择要为其创建管道的存储库。

    ![选择生成管道的存储库](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. 在 " **配置管道** " 页上，选择 " **Starter 管道**"。 如果你有要用于创建此管道的预先存在的 Azure Pipelines YAML 文件，则可以选择 **现有 AZURE PIPELINES YAML 文件** ，并向该文件提供存储库中的分支和路径。

    ![选择初学者管道或现有 Azure Pipelines YAML 文件以开始生成管道](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. 在 " **查看管道 YAML** " 页上，可以单击默认名称 `azure-pipelines.yml` 以重命名管道的配置文件。

   选择 " **显示助手** " 打开 " **任务** " 面板。

    ![选择 "显示助手" 打开任务面板](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. 要添加任务，请将光标置于 YAML 的末尾，或将光标放在要添加任务说明的位置。 搜索并选择 **Azure IoT Edge**。 按如下所示填写任务的参数。 然后选择“添加”。

   | 参数 | 说明 |
   | --- | --- |
   | 操作 | 选择 " **生成模块映像**"。 |
   | 文件 .template.js | 提供包含 IoT Edge 解决方案的存储库中文件的 **deployment.template.js** 路径。 |
   | 默认平台 | 根据目标 IoT Edge 设备为模块选择适当的操作系统。 |

    ![使用 "任务" 面板将任务添加到管道](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > 添加每个任务后，编辑器将自动突出显示添加的行。 若要防止意外覆盖，请在添加其他任务之前取消选择行并为下一个任务提供新的空间。

8. 重复此过程以添加三个具有以下参数的任务：

   * 任务： **Azure IoT Edge**

       | 参数 | 说明 |
       | --- | --- |
       | 操作 | 选择 " **推送模块映像**"。 |
       | 容器注册表类型 | 使用默认类型： **Azure 容器注册表**。 |
       | Azure 订阅 | 选择订阅。 |
       | Azure 容器注册表 | 选择要用于管道的注册表。 |
       | 文件 .template.js | 提供包含 IoT Edge 解决方案的存储库中文件的 **deployment.template.js** 路径。 |
       | 默认平台 | 根据目标 IoT Edge 设备为模块选择适当的操作系统。 |

   * 任务： **复制文件**

       | 参数 | 说明 |
       | --- | --- |
       | 源文件夹 | 要从中进行复制的源文件夹。 空为存储库的根。 如果文件不在存储库中，请使用变量。 示例：`$(agent.builddirectory)`。
       | 目录 | 添加两行： `deployment.template.json` 和 `**/module.json` 。 |
       | 目标文件夹 | 指定变量 `$(Build.ArtifactStagingDirectory)` 。 请参阅[生成变量](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables)，了解相关说明。 |

   * 任务： **发布生成项目**

       | 参数 | 说明 |
       | --- | --- |
       | 要发布的路径 | 指定变量 `$(Build.ArtifactStagingDirectory)` 。 请参阅[生成变量](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables)，了解相关说明。 |
       | 项目名称 | 指定默认名称： `drop` |
       | 项目发布位置 | 使用默认位置： `Azure Pipelines` |

9. 从右上方的 "**保存并运行**" 下拉列表中选择 "**保存**"。

10. 默认情况下，为 YAML 管道启用持续集成触发器。 若要编辑这些设置，请选择管道，然后单击右上方的 " **编辑** "。 在右上方的 "**运行**" 按钮旁选择 "**更多操作**"，然后单击 "**触发器**"。 **持续集成** 在管道名称下显示为 "已启用"。 若要查看触发器的详细信息，请选中 " **替代此处的 YAML 持续集成触发器"** 框。

    ![若要查看管道的触发器设置，请参阅 "更多操作" 下的触发器](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

继续下一部分以生成发布管道。

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>后续步骤

* [Azure DevOps Starter 中的](how-to-devops-starter.md)IoT Edge DevOps 最佳实践示例 IoT Edge
* 在[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)中了解 IoT Edge 部署
* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，详细了解创建、更新或删除部署的步骤。
