---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 706b2306fbe9f2a744d2874a8b55f78fa2fc8e4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89301688"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>创建用于持续部署的发布管道

在本部分中，会创建一个发布管道，此管道配置为在生成管道放置项目时自动运行，并且它将在 Azure Pipelines 中显示部署日志。

创建新管道并添加新阶段：

1. 在 "**管道**" 下的 "**发布**" 选项卡中，选择 " **+ 新建管道**"。 或者，如果已有发布管道，则选择“+ 新建”按钮并选择“+ 新建发布管道” 。  

    ![使用 "+ 新建管道" 按钮添加发布管道](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. 提示选择模板时，选择以“空作业”开头。

    ![从发布管道的空作业开始](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. 新版本管道初始化为一个阶段，称为“阶段 1”。 将阶段1重命名为 " **开发** "，并将其视为适用于你的开发环境的持续部署管道。 通常，连续部署管道有多个阶段，包括 **开发**、 **过渡**和 **生产**。你可以使用不同的名称，并根据 DevOps 的做法创建更多。 重命名后关闭阶段详细信息窗口。

   你还可以通过选择顶部的 "新发布管道" 文本来重命名发布管道。

4. 将发布链接到生成管道发布的生成项目。 在项目区域中，单击“添加”。

   ![单击界面的 "项目" 区域中的 "添加"](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. 在 " **添加项目" 页**上，选择 " **生成** " 作为 " **源类型**"。 选择所创建的项目和生成管道。 如果需要，可以将 **源别名** 更改为更具描述性的内容。 然后选择“添加”。

   ![在 "添加项目" 页上，选择 "添加" 以创建项目](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. 打开项目触发器并选择切换以启用持续部署触发器。 现在，每次有新的生成时，都会创建新版本。

   ![打开项目触发器并切换以启用持续部署触发器](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. “dev”阶段预先配置了一个作业和零个任务。 从管道菜单中，选择“任务”，然后选择“dev”阶段 。 选择 **代理作业** 并将其 **显示名称** 更改为 " **QA**"。 你可以配置有关代理作业的详细信息，但部署任务不区分平台，因此你可以使用所选**代理池中**的任何**代理规范**。

   ![在 "任务" 选项卡下查看开发阶段的任务](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. 在 QA 作业中，选择加号 (**+**) 以添加两个任务。 搜索并添加“Azure IoT Edge”两次。

9. 选择第一个 **Azure IoT Edge** 任务，并使用以下值对其进行配置：

    | 参数 | 说明 |
    | --- | --- |
    | 显示名称 | 当操作字段更改时，显示名称会自动更新。 |
    | 操作 | 选择 `Generate deployment manifest`。 |
    | 文件 .template.js | 指定路径： `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` 。 路径从生成管道发布。 |
    | 默认平台 | 根据目标 IoT Edge 设备为模块选择适当的操作系统。 |
    | 输出路径| 放置路径 `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`。 此路径是最终的 IoT Edge 部署清单文件。 |

    这些配置有助于替换 `deployment.template.json` 文件中的模块映像 URL。 “生成部署清单”也有助于将变量替换为在 `deployment.template.json` 文件中定义的具体值。 在 VS/VS Code 中，你是在 `.env` 文件中指定实际值。 在 Azure Pipelines 中，可以在 " **释放管道变量** " 选项卡中设置值。转到 " **变量** " 选项卡，并按如下所示配置名称和值：

    * **ACR_ADDRESS**： Azure 容器注册表 **登录服务器** 值。 可以在 Azure 门户的容器注册表的“概述”页中检索登录服务器。
    * **ACR_PASSWORD**：你的 Azure 容器注册表密码。
    * **ACR_USER**：你的 Azure 容器注册表用户名。

    如果项目中有其他变量，则可以在此选项卡中指定名称和值。 **生成部署清单** 只能识别具有风格的变量 `${VARIABLE}` 。 请确保在文件中使用这种风格 `*.template.json` 。

    ![在 "变量" 选项卡中配置发布管道的变量](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. 选择第二个 **Azure IoT Edge** 任务，并使用以下值对其进行配置：

    | 参数 | 说明 |
    | --- | --- |
    | 显示名称 | 当操作字段更改时，显示名称会自动更新。 |
    | 操作 | 选择 `Deploy to IoT Edge devices`。 |
    | 部署文件 | 放置路径 `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`。 此路径是 IoT Edge 部署清单文件的文件。 |
    | Azure 订阅 | 选择包含 IoT 中心的订阅。|
    | IoT 中心名称 | 选择 IoT 中心。|
    | 选择单/多设备 | 选择是否希望发布管道部署到一个或多个设备。 如果部署到单个设备，请输入“IoT Edge设备 ID”。 如果要部署到多个设备，请指定设备“目标条件”。 目标条件是用于在 IoT 中心匹配一组 IoT Edge 设备的筛选器。 如果要使用设备标记作为条件，需使用 IoT 中心设备克隆更新相应的设备标记。 在高级设置中更新“IoT Edge 部署 ID”和“IoT Edge 部署优先级” 。 有关为多个设备创建部署的详细信息，请参阅[了解 IoT Edge 自动部署](../articles/iot-edge/module-deployment-monitoring.md)。 |
    | 设备 ID 或目标条件 | 根据前面的选择，指定要部署到多个设备的设备 ID 或 [目标条件](../articles/iot-edge/module-deployment-monitoring.md#target-condition) 。 |
    | 高级 | 对于 "IoT Edge 部署 ID"，请指定 `$(System.TeamProject)-$(Release.EnvironmentName)` 。 此变量将项目和发布名称映射为你的 IoT Edge 部署 ID。 |

    ![为开发阶段添加 Azure IoT Edge 任务](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. 选择“保存”，将更改保存到新发布管道。 从菜单中选择 " **管道** " 选项卡，返回到 "管道" 视图。
