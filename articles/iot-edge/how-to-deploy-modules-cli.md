---
title: 部署 Azure IoT Edge 模块 (CLI) | Microsoft Docs
description: 通过适用于 Azure CLI 2.0 的 IoT 扩展将模块部署到 IoT Edge 设备
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/08/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 02a7533cf1c06142f564ed9fd6008a25be9fc603
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034338"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli-20"></a>使用 Azure CLI 2.0 部署 Azure IoT Edge 模块

使用业务逻辑创建 IoT Edge 模块后，需要将其部署到设备后才能在边缘操作。 如果多个模块共同协作来收集和处理数据，可同时部署它们并声明用于连接它们的路由规则。 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 是一个开源跨平台命令行工具，用于管理 IoT Edge 等 Azure 资源。 使用 Azure CLI 2.0 可以管理 Azure IoT 中心资源、设备预配服务实例和现成的链接中心。 新的 IoT 扩展丰富了 Azure CLI 2.0 的功能，例如设备管理和完整的 IoT Edge 功能。

本文介绍了如何创建 JSON 部署清单，然后使用此文件将部署推送至 IoT Edge 设备。 要了解如何创建基于设备的共享标记而面向多台设备的部署，请参阅[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device-cli.md)。
* 环境中的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 2.0 版本必须至少是 2.0.24 或更高。 请使用 `az –-version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 
* [适用于 Azure CLI 2.0 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 若要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

若要使用 Azure CLI 2.0 来部署模块，请将部署清单在本地另存为 .txt 文件。 在下一部分通过运行命令将配置应用到设备时，会用到这个文件路径。 

下面是一个基本的部署清单示例，其中有一个模块：

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {
                 "registryName": {
                   "username": "",
                   "password": "",
                   "address": ""
                 }
               }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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

## <a name="deploy-to-your-device"></a>部署到设备

应用使用模块信息配置的部署清单即可将模块部署至设备。 

使用以下命令将配置应用于 IoT Edge 设备：

   ```cli
   az iot hub apply-configuration --device-id [device id] --hub-name [hub name] --content [file path]
   ```

device id 参数区分大小写。 content 参数指向你保存的部署清单文件。 

## <a name="view-modules-on-your-device"></a>查看设备上的模块

将模块部署到设备后，可以使用以下命令查看所有模块： 

查看 IoT Edge 设备上的模块：
    
   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

device id 参数区分大小写。

   ![列出模块](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>后续步骤

了解如何[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)