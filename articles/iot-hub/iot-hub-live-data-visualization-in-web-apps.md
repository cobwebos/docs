---
title: Azure IoT 中心的实时传感器数据可视化 – Web 应用 | Microsoft Docs
description: 使用 Microsoft Azure 应用服务的 Web 应用功能可视化从传感器收集并发送到 IoT 中心的温度和湿度数据。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 070f37a969411cfc4caf5f2d2b089ccfae759ca2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125706"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>使用 Azure 应用服务的 Web 应用功能可视化 Azure IoT 中心的实时传感器数据

![端到端关系图](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学习内容

本教程讲解如何通过运行一个托管在 Web 应用上的 Web 应用程序来可视化 IoT 中心接收的实时传感器数据。 若要尝试使用 Power BI 可视化 IoT 中心的数据，请参阅[使用 Power BI 可视化 Azure IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-power-bi.md)。

## <a name="what-you-do"></a>准备工作

* 在 Azure 门户中创建 Web 应用。
* 添加一个使用者组，让 IoT 中心做好数据访问准备。
* 将 Web 应用配置为从 IoT 中心读取传感器数据。
* 上传要在 Web 应用中托管的 Web 应用程序。
* 打开该 Web 应用，查看 IoT 中心的实时温度和湿度数据。

## <a name="what-you-need"></a>所需条件

* 完成[Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或其中一个设备教程中; 例如， [Raspberry Pi 与 node.js 配合使用](iot-hub-raspberry-pi-kit-node-get-started.md)。 这些涵盖以下要求：

  * 一个有效的 Azure 订阅
  * 已在订阅中创建一个 IoT 中心
  * 一个可向 IoT 中心发送消息的客户端应用程序

* [下载 Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>创建 Web 应用

1. 在 [Azure 门户](https://portal.azure.com/)中，单击“创建资源” > “Web + 移动” > “Web 应用”。

2. 输入唯一的作业名称，验证订阅，指定资源组和位置，选择“固定到仪表板”，并单击“创建”。

   建议选择与资源组相同的位置。 
   
   ![创建 Web 应用](./media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>将 Web 应用配置为从 IoT 中心读取数据

1. 打开刚刚预配的 Web 应用。

2. 单击“应用程序设置”，并在“应用设置”下面添加以下键/值对：

   | 密钥                                   | 值                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | 已从 Azure CLI 获取                                      |
   | Azure.IoT.IoTHub.ConsumerGroup        | 添加到 IoT 中心的使用者组的名称  |
   | WEBSITE_NODE_DEFAULT_VERSION          | 8.9.4                                                        |

   ![将包含键/值对的设置添加到 Web 应用](./media/iot-hub-live-data-visualization-in-web-apps/3_web-app-settings-key-value-azure.png)

3. 单击“常规设置”下的“应用程序设置”，切换“Web 套接字”选项，然后单击“保存”。

   ![切换 Web 套接字选项](./media/iot-hub-live-data-visualization-in-web-apps/4_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>上传要在 Web 应用中托管的 Web 应用程序

我们在 GitHub 上提供了一个 Web 应用程序，它可以显示 IoT 中心的实时传感器数据。 只需将 Web 应用配置为使用 Git 存储库，从 GitHub 下载该 Web 应用程序，然后将其上传到 Azure，以便在 Web 应用中托管。

1. 在 Web 应用中，单击“部署选项” > “选择源” > “本地 Git 存储库”，然后单击“确定”。

   ![将 Web 应用部署配置为使用本地 Git 存储库](./media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. 单击“部署凭据”，创建用于连接到 Azure 中 Git 存储库的用户名和密码，然后单击“保存”。

3. 单击“概述”并记下“Git clone URL”的值。

   ![获取 Web 应用的“Git clone URL”](./media/iot-hub-live-data-visualization-in-web-apps/6_web-app-git-clone-url-azure.png)

4. 在本地计算机上打开命令提示符或终端窗口。

5. 从 GitHub 下载 Web 应用并将它上传到 Azure，以便在 Web 应用中托管。 要执行此项操作，请运行以下命令：

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > “Git 克隆 URL”是 Web 应用的“概述”页中显示的 Git 存储库的 URL。\<\>

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>打开该 Web 应用，查看 IoT 中心的实时温度和湿度数据

在 Web 应用的“概述”页上，单击 URL 打开 Web 应用。

![获取 Web 应用的 URL](./media/iot-hub-live-data-visualization-in-web-apps/7_web-app-url-azure.png)

此时应会看到 IoT 中心的实时温度和湿度数据。

![显示实时温度和湿度的 Web 应用页](./media/iot-hub-live-data-visualization-in-web-apps/8_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> 确保示例应用程序正在设备上运行。 如果没有，将会得到空白图表，可参考[设置设备](iot-hub-raspberry-pi-kit-node-get-started.md)中的教程。

## <a name="next-steps"></a>后续步骤

现已成功使用 Web 应用可视化 IoT 中心的实时传感器数据。

若要了解另一种可视化 Azure IoT 中心数据的方式，请参阅[使用 Power BI 可视化 IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-power-bi.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]