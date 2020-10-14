---
title: 从 Azure CLI 命令行部署模块 - Azure IoT Edge
description: 将 Azure CLI 与 Azure IoT 扩展结合使用，以将 IoT Edge 模块从 IoT 中心推送到 IoT Edge 设备（按照部署清单的配置）。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 86cccbc9a72459ad038defca32e232381368ef45
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046683"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>使用 Azure CLI 部署 Azure IoT Edge 模块

使用业务逻辑创建 IoT Edge 模块后，需要将其部署到设备后才能在边缘操作。 如果多个模块共同协作来收集和处理数据，可同时部署它们并声明用于连接它们的路由规则。

[Azure CLI](/cli/azure) 是一个开源跨平台命令行工具，用于管理 IoT Edge 等 Azure 资源。 使用 Azure CLI 2.0 可以管理 Azure IoT 中心资源、设备预配服务实例和现成的链接中心。 新的 IoT 扩展丰富了 Azure CLI 的功能，例如设备管理和完整的 IoT Edge 功能。

本文介绍了如何创建 JSON 部署清单，然后使用此文件将部署推送至 IoT Edge 设备。 要了解如何创建基于设备的共享标记而面向多台设备的部署，请参阅[大规模地部署和监视 IoT Edge 模块](how-to-deploy-cli-at-scale.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。
* IoT Edge 设备

  如果未设置 IoT Edge 设备，则可以在 Azure 虚拟机中创建一个。 按照其中一篇快速入门文章中的步骤 [创建虚拟 Linux 设备](quickstart-linux.md) ，或 [创建虚拟 Windows 设备](quickstart.md)。

* 环境中的 [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI 版本必须至少是 2.0.70 或更高版本。 请使用 `az --version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 若要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

若要使用 Azure CLI 来部署模块，请将部署清单在本地另存为 .json 文件。 在下一部分通过运行命令将配置应用到设备时，会用到这个文件路径。

下面是一个基本的部署清单示例，其中有一个模块：

>[!NOTE]
>此示例部署清单使用架构版本1.1 作为 IoT Edge 代理和中心。 架构版本1.1 与 IoT Edge 版本1.0.10 一起发布，并启用了模块启动顺序和路由优先级等功能。

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
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
          "schemaVersion": "1.1",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>部署到设备

应用使用模块信息配置的部署清单即可将模块部署至设备。

将目录更改到保存有部署清单的文件夹。 如果使用了 VS Code IoT Edge 模板之一，请使用解决方案目录的 **config** 文件夹中的 `deployment.json` 文件，而不是使用 `deployment.template.json` 文件。

使用以下命令将配置应用于 IoT Edge 设备：

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

device ID 参数区分大小写。 content 参数指向你保存的部署清单文件。

   ![az iot edge set-modules output](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>查看设备上的模块

将模块部署到设备后，可以使用以下命令查看所有模块：

查看 IoT Edge 设备上的模块：

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

device ID 参数区分大小写。

   ![az iot hub module-identity list output](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>后续步骤

了解如何[大规模地部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)