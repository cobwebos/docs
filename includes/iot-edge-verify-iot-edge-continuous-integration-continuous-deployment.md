---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: c7be10e14daf3ee769e86d51f648cc6b656a416a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89301708"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用生成和发布管道验证 IoT Edge CI/CD

要触发生成作业，可以将提交推送到源代码存储库或手动触发该作业。 在本部分中，手动触发 CI/CD 管道以测试它是否正常工作。 然后验证部署是否成功。

1. 在左窗格菜单中，选择 " **管道** "，并打开在本文开头创建的生成管道。

2. 通过选择右上方的 " **运行管道** " 按钮，可以在生成管道中触发生成作业。

    ![使用 "运行管道" 按钮手动触发生成管道](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. 查看 **运行管道** 设置。 然后选择 " **运行**"。

    ![指定运行管道选项，然后选择 "运行"](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. 选择 " **代理作业 1** "，观看运行进度。 您可以通过选择作业来查看作业的输出日志。 

    ![查看作业的日志输出](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. 如果生成管道成功完成，则会触发到“dev”阶段的发布。 **dev** 发布成功以后，会创建 IoT Edge 部署，部署到目标 IoT Edge 设备。

    ![发布到 dev](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. 单击 **dev** 阶段即可查看发布日志。

    ![发布日志](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)