---
title: 使用 Azure CLI 2.0 的 IoT 扩展将模块部署到 IoT Edge 设备 | Microsoft Docs
description: 使用 Azure CLI 2.0 的 IoT 扩展将模块部署到 IoT Edge 设备
author: chrissie926
manager: ''
ms.author: menchi
ms.date: 03/02/2018
ms.topic: tutorial
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
md.custom: mvc
ms.openlocfilehash: deee54fe5d11d6d1cf5485357f853b1cb078f96d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631575"
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>使用 Azure CLI 2.0 的 IoT 扩展将模块部署到 IoT Edge 设备

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 是一个开源跨平台命令行工具，用于管理 IoT Edge 等 Azure 资源。 Azure CLI 2.0 适用于 Windows、Linux 和 MacOS。

使用 Azure CLI 2.0 可以管理 Azure IoT 中心资源、设备预配服务实例和现成的链接中心。 新的 IoT 扩展丰富了 Azure CLI 2.0 的功能，例如设备管理和完整的 IoT Edge 功能。

在本文中，将安装 Azure CLI 2.0 和 IoT 扩展。 然后，了解如何使用可用的 CLI 命令将模块部署到 IoT Edge 设备。

## <a name="prerequisites"></a>先决条件

* 一个 Azure 帐户。 如果还没有 Azure 帐户，可以立即[创建一个免费帐户](https://azure.microsoft.com/free/?v=17.39a)。 

* [Python 2.7x 或 Python 3.x](https://www.python.org/downloads/)。

* 环境中的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 2.0 版本必须至少是 2.0.24 或更高。 请使用 `az –-version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 在 Windows 上，一种简单的安装方法是下载并安装 [MSI](https://aka.ms/InstallAzureCliWindows)。

* [适用于 Azure CLI 2.0 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)：
   1. 运行 `az extension add --name azure-cli-iot-ext`。 
   2. 安装完成后，可以使用 `az extension list` 验证当前安装的扩展，或使用 `az extension show --name azure-cli-iot-ext` 查看有关 IoT 扩展的详细信息。
   3. 若要删除该扩展，请使用 `az extension remove --name azure-cli-iot-ext`。


## <a name="create-an-iot-edge-device"></a>创建 IoT Edge 设备
本文提供用于创建 IoT Edge 部署的说明。 该示例演示如何登录到 Azure 帐户、创建 Azure 资源组（保存 Azure 解决方案相关资源的容器）、创建 IoT 中心、创建三个 IoT Edge 设备标识、设置标记，然后创建针对这些设备的 IoT Edge 部署。 

登录到 Azure 帐户。 输入以下登录命令后，系统会提示你通过 Web 浏览器使用一次性代码登录： 

   ```cli
   az login
   ```

在美国东部区域中创建名为 **IoTHubCLI** 的新资源组： 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![创建资源组][2]

在新建的资源组中创建名为 **CLIDemoHub** 的 IoT 中心：

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >每个订阅会被分配一个免费 IoT 中心。 若要使用 CLI 命令创建免费中心，请将 SKU 值替换为 `--sku F1`。 如果订阅中已有免费中心，尝试创建第二个免费中心时，你将收到一条错误消息。 

创建 IoT Edge 设备：

   ```cli
   az iot hub device-identity create --device-id edge001 --hub-name CLIDemoHub --edge-enabled
   ```

   ![创建 IoT Edge 设备][4]

## <a name="configure-the-iot-edge-device"></a>配置 IoT Edge 设备

创建部署 JSON 模板，并将其作为 txt 文件保存在本地。 运行 apply-configuration 命令时，需要指定该文件的路径。

部署 JSON 模板应始终包含两个系统模块：edgeAgent 和 edgeHub。 除了这两个模块外，还可以使用此文件将其他模块部署到 IoT Edge 设备。 按照以下示例使用一个 tempSensor 模块配置 IoT Edge 设备：

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

将配置应用到 IoT Edge 设备：

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

查看 IoT Edge 设备上的模块：
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![列出模块][6]

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 IoT Edge 设备作为网关](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

