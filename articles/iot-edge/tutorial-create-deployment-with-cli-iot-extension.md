---
title: "使用 Azure CLI 2.0 的 IoT 扩展将模块部署到 IoT Edge 设备 | Microsoft Docs"
description: "使用 Azure CLI 2.0 的 IoT 扩展将模块部署到 IoT Edge 设备"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>使用 Azure CLI 2.0 的 IoT 扩展将模块部署到 IoT Edge 设备

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) 是一个开源跨平台命令行工具，用于管理 IoT Edge 等 Azure 资源。 Azure CLI 2.0 适用于 Windows、Linux 和 MacOS。

使用 Azure CLI 2.0 可以管理 Azure IoT 中心资源、设备预配服务实例和现成的链接中心。 新的 IoT 扩展丰富了 Azure CLI 2.0 的功能，例如设备管理和完整的 IoT Edge 功能。

在本教程中，我们先完成设置 Azure CLI 2.0 和 IoT 扩展的步骤。 然后，了解如何使用可用的 CLI 命令将模块部署到 IoT Edge 设备。

## <a name="installation"></a>安装 

### <a name="step-1---install-python"></a>步骤 1 - 安装 Python

需要 [Python 2.7x 或 Python 3.x](https://www.python.org/downloads/)。

### <a name="step-2---install-azure-cli-20"></a>步骤 2 - 安装 Azure CLI 2.0

遵照[安装说明](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)在环境中安装 Azure CLI 2.0。 Azure CLI 2.0 版本必须至少是 2.0.24 或更高。 请使用 `az –version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 在 Windows 上，一种简单的安装方法是下载并安装 [MSI](https://aka.ms/InstallAzureCliWindows)。

### <a name="step-3---install-iot-extension"></a>步骤 3 - 安装 IoT 扩展

[IoT 扩展自述文件](https://github.com/Azure/azure-iot-cli-extension)介绍了该扩展的多种安装方法。 最简单的方法是运行 `az extension add --name azure-cli-iot-ext`。 安装完成后，可以使用 `az extension list` 验证当前安装的扩展，或使用 `az extension show --name azure-cli-iot-ext` 查看有关 IoT 扩展的详细信息。 若要删除该扩展，可以使用 `az extension remove --name azure-cli-iot-ext`。


## <a name="deploy-modules-to-an-iot-edge-device"></a>将模块部署到 IoT Edge 设备
本教程将会介绍如何创建 IoT Edge 部署。 该示例演示如何登录到 Azure 帐户、创建 Azure 资源组（保存 Azure 解决方案相关资源的容器）、创建 IoT 中心、创建三个 IoT Edge 设备标识、设置标记，然后创建针对这些设备的 IoT Edge 部署。 在开始之前，请完成前面所述的安装步骤。 如果没有 Azure 帐户，可以立即[创建一个免费帐户](https://azure.microsoft.com/free/?v=17.39a)。 


### <a name="1-login-to-the-azure-account"></a>1.登录到 Azure 帐户
  
    az login

![登录][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2.在 eastus 中创建资源组 IoTHubBlogDemo

    az group create -l eastus -n IoTHubBlogDemo

![创建资源组][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>3.在新建的资源组下创建 IoT 中心 blogDemoHub

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![创建 IoT 中心][3]


### <a name="4-create-an-iot-edge-device"></a>4.创建 IoT Edge 设备

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![创建 IoT Edge 设备][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5.将配置应用到 IoT Edge 设备

以 txt 文件格式将 JSON 部署模板保存在本地。 运行 apply-configuration 命令时，需要指定该文件的路径。

下面是一个示例 JSON 部署模板，其中包含一个 tempSensor 模块：

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
            "loggingOptions": ""
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
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
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
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

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![应用配置][5]

### <a name="6-list-modules"></a>6.列出模块
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![列出模块][6]

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了 Azure Functions，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 若要继续探索 Azure IoT Edge，请学习如何将 IoT Edge 用作网关。 

> [!div class="nextstepaction"]
> [创建 IoT Edge 网关设备](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

