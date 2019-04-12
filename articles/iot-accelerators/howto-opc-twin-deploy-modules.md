---
title: 如何从零开始的 Azure 部署 OPC 孪生模块 |Microsoft Docs
description: 如何将 OPC 孪生部署从零开始。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f470beb79e69b5a4a3febeb6a433c48490b96cf7
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491350"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>部署 OPC 孪生模块从零开始的依赖项

OPC 孪生模块在 IoT Edge 上运行，并提供了多个边缘服务添加到 OPC 设备孪生和注册表服务。 

有几个选项来部署模块到您[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/)网关，在它们之间

- [部署从 Azure 门户的 IoT Edge 边栏选项卡](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [使用 AZ CLI 进行部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> 部署详细信息和说明的详细信息，请参阅 GitHub[存储库](https://github.com/Azure/azure-iiot-components)。

## <a name="deployment-manifest"></a>部署清单

使用部署清单部署所有模块。  若要同时部署的示例清单[OPC 发布服务器](https://github.com/Azure/iot-edge-opc-publisher)并[OPC 孪生](https://github.com/Azure/azure-iiot-opc-twin-module)如下所示。

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
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
            }
          }
        },
        "modules": {
          "opctwin": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
              "createOptions": "{\"HostConfig\": { \"NetworkMode\": \"host\", \"CapAdd\": [\"NET_ADMIN\"] } }"
            }
          },
          "opcpublisher": {
            "version": "2.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
              "createOptions": "{\"Hostname\": \"publisher\", \"Cmd\": [ \"publisher\", \"--pf=./pn.json\", \"--di=60\", \"--to\", \"--aa\", \"--si=0\", \"--ms=0\" ], \"ExposedPorts\": { \"62222/tcp\": {} }, \"HostConfig\": { \"PortBindings\": { \"62222/tcp\": [{ \"HostPort\": \"62222\" }] } } }"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
          "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>从 Azure 门户部署

将模块部署到 Azure IoT Edge 网关设备的最简单方法是通过 Azure 门户。  

### <a name="prerequisites"></a>必备组件

1. 部署 OPC 孪生[依赖项](howto-opc-twin-deploy-dependencies.md)获取所生成和`.env`文件。 请注意已部署`hub name`的`PCS_IOTHUBREACT_HUB_NAME`变量中生成`.env`文件。

2. 注册并启动[Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)或[Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge 网关并记下及其`device id`。

### <a name="deploy-to-an-edge-device"></a>部署到 edge 设备

1. 登录 [Azure 门户](https://portal.azure.com/)，导航到 IoT 中心。

2. 选择**IoT Edge**左侧的菜单。

3. 在设备列表中单击目标设备的 ID。

4. 选择“设置模块”。

5. 在中**部署模块**部分中的页上，选择**添加**和**IoT Edge 模块。**

6. 在中**IoT Edge Custom Module**对话框中使用`opctwin`作为模块的名称，然后指定容器*图像 URI*作为

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   作为*创建选项*使用以下 JSON:

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   必要时请填写可选字段。 要详细了解容器创建选项、重启策略和所需状态，请参阅 [EdgeAgent 必需属性](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties)。 要详细了解模块孪生，请参阅[定义或更新所需属性](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)。

7. 选择**保存**并重复执行步骤**5**。  

8. 在 IoT Edge Custom Module 对话框中，使用`opcpublisher`作为名称的模块，并在容器*映像 URI*作为 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   作为*创建选项*使用以下 JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. 选择**保存**，然后**下一步**继续路由部分。

10. 在路由选项卡中，粘贴以下 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    然后选择**下一步**

11. 审阅部署信息和清单。  它应类似于上面的部署清单。  选择“提交”。

12. 将模块部署到设备之后，即可在门户的“设备详细信息”页中查看所有模块。 此页面显示每个已部署模块的名称，以及部署状态和退出代码等有用信息。

## <a name="deploying-using-azure-cli"></a>使用 Azure CLI 进行部署

### <a name="prerequisites"></a>必备组件

1. 安装最新版本[Azure 命令行界面 (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)从[此处](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

### <a name="quickstart"></a>快速入门

1. 保存到更高版本的部署清单`deployment.json`文件。  

2. 使用以下命令将配置应用于 IoT Edge 设备：

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   `device id`参数是区分大小写。 content 参数指向你保存的部署清单文件。 
    ![az IoT Edge 模块集输出](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. 将模块部署到设备后，可以使用以下命令查看所有模块：

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   设备 ID 参数是区分大小写。 ![az iot hub module-identity list output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="run-and-debug-locally"></a>本地运行和调试

对于故障排除和调试它非常有用若要运行的 Edge 模块，使用本地[IoT Edge 开发模拟器](https://github.com/Azure/iotedgehubdev)。  它与模拟器中提供的本地开发体验的创建、 开发、 测试、 运行和调试 Azure IoT Edge 模块并使用相同的位或代码在生产环境中使用的解决方案。

### <a name="prerequisites"></a>必备组件

1. 部署 OPC 孪生[依赖项](howto-opc-twin-deploy-dependencies.md)。

2. 安装[Docker CE (18.02.0+)](https://www.docker.com/community-edition)上[Windows](https://docs.docker.com/docker-for-windows/install/)， [macOS](https://docs.docker.com/docker-for-mac/install/)或者[Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)。

3. 安装[Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (才需要**Linux**。 Compose 已包含在 Windows/macOS Docker CE 安装)

4. 安装[Python (2.7 / 3.5+) 和 Pip](https://www.python.org/)

5. 通过运行以下命令在终端中安装 iotedgehubdev

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> 安装`iotedgehubdev`到**根**Linux/macOS 上 (*不使用-用户中 pip install 命令选项*)。
> 请确保没有 iotedgehubdev 与在同一台计算机上运行，因为它们需要相同的端口的 Azure IoT Edge 运行时。

### <a name="quickstart"></a>快速入门

1. 按照说明[Azure 门户中创建 Edge 设备](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)。  将复制 edge 设备连接字符串。

2. 设置模拟器使用 edge 连接字符串。

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. 复制上面到清单`deployment.json`同一文件夹中的文件。  使用在模拟器中启动的部署

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. 停止模拟器使用

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>后续步骤

现在，已了解如何部署从零开始的 OPC 孪生，下面是建议的下一步：

> [!div class="nextstepaction"]
> [将 OPC 孪生部署到现有项目](howto-opc-twin-deploy-existing.md)