---
title: 部署 Azure IoT Edge 模块 (VS Code) | Microsoft Docs
description: 使用 Visual Studio Code 将模块部署至 IoT Edge 设备
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5867012e0322e520b4ef234599fa25a2ec08138d
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325762"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>通过 Visual Studio Code 部署 Azure IoT Edge 模块

使用业务逻辑创建 IoT Edge 模块后，需要将其部署到设备后才能在边缘操作。 如果多个模块共同协作来收集和处理数据，可同时部署它们并声明用于连接它们的路由规则。 

本文介绍如何创建 JSON 部署清单，然后使用此文件将部署推送至 IoT Edge 设备。 要了解如何创建基于设备的共享标记而面向多台设备的部署，请参阅[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device-portal.md)。 
* [Visual Studio Code](https://code.visualstudio.com/)。
* 适用于 Visual Studio Code 的 [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

若要使用 Visual Studio Code 部署模块，请将部署清单本地保存为 .JSON 文件。 在下一节中运行命令来将配置应用到设备时，会用到这个文件路径。

下面是一个基本的部署清单示例，其中具有一个模块：

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```
   
## <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

可使用适用于 Visual Studio Code 的 Azure IoT 扩展来执行与 IoT 中心相关的操作。 为让操作顺利进行，需登录 Azure 帐户并选择要使用的 IoT 中心。

1. 在 Visual Studio Code 中打开“资源管理器”视图。

2. 在资源管理器底部，展开“Azure IoT 中心设备”部分。 

   ![展开 Azure IoT 中心设备](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. 单击“Azure IoT 中心设备”部分标题中的“...”。 如果没有看到省略号，请将鼠标悬停在标题处。 

4. 选择“选择 IoT 中心”。

5. 如果尚未登录 Azure 帐户，请按照提示登录。 

6. 选择 Azure 订阅。 

7. 选择 IoT 中心。 


## <a name="deploy-to-your-device"></a>部署到设备

应用使用模块信息配置的部署清单即可将模块部署至设备。 

1. 在 Visual Studio Code 资源管理器视图中，展开“Azure IoT 中心设备”部分。 

2. 右键单击想使用部署清单配置的设备。 

3. 选择“创建 IoT Edge 设备的部署”。 

4. 导航至要使用的部署清单 JSON 文件，然后单击“选择 Edge 部署清单”。 

   ![选择 Edge 部署清单](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


部署结果已显示在 VS Code 输出内容中。 如果目标设备正在运行且连接到了 Internet，则会在几分钟内成功应用部署。 

## <a name="view-modules-on-your-device"></a>查看设备上的模块

将模块部署至设备后，可在“Azure IoT 中心设备”部分查看所有模块。 选择 IoT Edge 设备旁边的箭头将其展开。 其中会显示当前正在运行的所有模块。 

如果最近部署了新模块到设备，请将鼠标悬停在“Azure IoT 中心设备”部分标题处，并选择刷新图标以更新该视图。 

右键单击模块名称，查看并编辑此模块孪生。 

## <a name="next-steps"></a>后续步骤

了解如何[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)