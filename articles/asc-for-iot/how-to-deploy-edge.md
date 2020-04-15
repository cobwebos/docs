---
title: 部署 IoT 边缘安全模块
description: 了解如何在 IoT 边缘部署 IoT 安全代理的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 4dd7ca8f926862487b9505731c0662e68ee3d7c0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311278"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>在 IoT 边缘设备上部署安全模块

**IoT Azure 安全中心**模块为 IoT 边缘设备提供了全面的安全解决方案。
安全模块收集、聚合和分析来自操作系统和容器系统的原始安全数据，并将其转化为可操作的安全建议和警报。
要了解更多信息，请参阅[IoT 边缘的安全模块](security-edge-architecture.md)。

在本文中，您将了解如何在 IoT Edge 设备上部署安全模块。

## <a name="deploy-security-module"></a>部署安全模块

使用以下步骤为 IoT 边缘部署 IoT 安全模块的 Azure 安全中心。

### <a name="prerequisites"></a>先决条件

1. 在 IoT 中心中，请确保您的设备[已注册为 IoT 边缘设备](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)。

1. IoT 边缘模块的 Azure 安全中心要求在 IoT 边缘设备上安装[AuditD 框架](https://linux.die.net/man/8/auditd)。

    - 通过在 IoT Edge 设备上运行以下命令来安装框架：

    `sudo apt-get install auditd audispd-plugins`

    - 通过运行以下命令验证 AuditD 处于活动状态：

    `sudo systemctl status auditd`<br>
    - 预期响应是：`active (running)`

### <a name="deployment-using-azure-portal"></a>使用 Azure 门户进行部署

1. 从 Azure 门户打开**应用商店**。

1. 选择**物联网**，然后搜索 IoT 的**Azure 安全中心**并选择它。

   ![为 IoT 选择 Azure 安全中心](media/howto/edge-onboarding-8.png)

1. 单击 **"创建**"以配置部署。

1. 选择 IoT 中心的 Azure**订阅**，然后选择**IoT 中心**。<br>选择 **"部署到设备**"以单个设备为目标，或选择"**在缩放中部署**"以定位多个设备，然后单击"**创建**"。 有关大规模部署的详细信息，请参阅[如何部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)。

    >[!Note]
    >如果选择 **"在缩放中部署"，** 请先添加设备名称和详细信息，然后再继续在以下说明中的"**添加模块"** 选项卡。

完成每个步骤，完成适用于 IoT 的 Azure 安全中心的 IoT 边缘部署。

#### <a name="step-1-modules"></a>第 1 步：模块

1. 选择**Azure 安全中心forIoT**模块。
1. 在"**模块设置"** 选项卡上，将**名称**更改为**azureaiotsecurity**。
1. 在"**环境变量"** 选项卡上，根据需要添加变量（例如，调试级别）。
1. 在 **"容器创建选项**"选项卡上，添加以下配置：

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. 在 **"模块孪生设置"** 选项卡上，添加以下配置：

    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
    ```

1. 选择“更新”  。

#### <a name="step-2-runtime-settings"></a>第 2 步：运行时设置

1. 选择**运行时设置**。
1. 在 **"边中心**"下，将**图像**更改为**mcr.microsoft.com/azureiotedge-hub:1.0.8.3**。
1. 验证**创建选项**设置为以下配置：

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

1. 选择“保存”。 

1. 选择“**下一页**”。

#### <a name="step-3-specify-routes"></a>第 3 步：指定路由

1. 在 **"指定路由"** 选项卡上，请确保具有路由（显式或隐式），该路由将根据以下示例将来自**azureiotaa）** 模块的消息转发到 **$upstream。** 仅当路径就位时，才选择 **"下一步**"。

   路由示例：

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. 选择“**下一页**”。

#### <a name="step-4-review-deployment"></a>第 4 步：查看部署

- 在"**查看部署"** 选项卡上，查看部署信息，然后选择 **"创建**"以完成部署。

## <a name="diagnostic-steps"></a>诊断步骤

如果遇到问题，容器日志是了解 IoT Edge 安全模块设备状态的最佳方式。 可以使用本部分中的命令和工具来收集信息。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>验证所需的容器已安装并按预期运行

1. 在 IoT 边缘设备上运行以下命令：

    `sudo docker ps`

1. 验证以下容器是否正在运行：

   | 名称 | IMAGE |
   | --- | --- |
   | azureiot 安全 | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | 边缘Hub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | 边缘代理 | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   如果不存在所需的最小容器，请检查 IoT Edge 部署清单是否与建议的设置一致。 有关详细信息，请参阅部署[IoT 边缘模块](#deployment-using-azure-portal)。

### <a name="inspect-the-module-logs-for-errors"></a>检查模块日志是否存在错误

1. 在 IoT 边缘设备上运行以下命令：

   `sudo docker logs azureiotsecurity`

1. 有关更详细的日志，将以下环境变量添加到**azureiotasecurity**模块部署： `logLevel=Debug`。

## <a name="next-steps"></a>后续步骤

要了解有关配置选项的详细信息，请继续访问模块配置的操作指南。
> [!div class="nextstepaction"]
> [模块配置操作指南](./how-to-agent-configuration.md)
