---
title: 如何从头开始为 Azure 部署 OPC 双子模块 |微软文档
description: 本文介绍如何使用 Azure 门户的 IoT 边缘边边边边边选项卡以及使用 AZ CLI 从头开始部署 OPC Twin。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241060"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>从头开始部署 OPC 双子模块和依赖项

OPC 双子模块在 IoT 边缘上运行，并为 OPC 设备孪生和注册表服务提供多项边缘服务。 

有几个选项可以将模块部署到[Azure IoT 边缘](https://azure.microsoft.com/services/iot-edge/)网关，其中包括

- [从 Azure 门户的 IoT 边缘边边边边边边边选项卡进行部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [使用 AZ CLI 进行部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> 有关部署详细信息和说明的详细信息，请参阅 GitHub[存储库](https://github.com/Azure/azure-iiot-components)。

## <a name="deployment-manifest"></a>部署清单

所有模块都使用部署清单部署。  下面显示了部署[OPC 发布服务器](https://github.com/Azure/iot-edge-opc-publisher)和[OPC 孪生](https://github.com/Azure/azure-iiot-opc-twin-module)的示例清单。

```json
{
  "content": {
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
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>从 Azure 门户部署

将模块部署到 Azure IoT 边缘网关设备的最简单方法是通过 Azure 门户。  

### <a name="prerequisites"></a>先决条件

1. 部署 OPC 孪生[依赖项](howto-opc-twin-deploy-dependencies.md)并获取`.env`生成的文件。 请注意在生成的`hub name``.env`文件中部署`PCS_IOTHUBREACT_HUB_NAME`的变量。

2. 注册并启动[Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)或[Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT 边缘`device id`网关并记下其 。

### <a name="deploy-to-an-edge-device"></a>部署到边缘设备

1. 登录到 Azure[门户](https://portal.azure.com/)并导航到 IoT 中心。

2. 从左侧菜单中选择**IoT 边缘**。

3. 在设备列表中单击目标设备的 ID。

4. 选择**设置模块**。

5. 在页面的 **"部署"模块**部分中，选择 **"添加**"和 **"IoT 边缘"模块。**

6. 在**IoT 边缘自定义模块**对话框`opctwin`中用作模块的名称，然后将容器*映像 URI*指定为

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   作为*容器创建选项*，请使用以下 JSON：

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   必要时请填写可选字段。 要详细了解容器创建选项、重启策略和所需状态，请参阅 [EdgeAgent 必需属性](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties)。 要详细了解模块孪生，请参阅[定义或更新所需属性](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)。

7. 选择 **"保存"** 并重复步骤**5**。  

8. 在 IoT 边缘自定义模块对话框中`opcpublisher`，用作模块的名称，将容器*映像 URI*用作 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   作为*容器创建选项*，请使用以下 JSON：

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. 选择 **"保存****"，然后选择"下一步**"以继续到路径部分。

10. 在路由选项卡中，粘贴以下内容 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    并选择 **"下一步"**

11. 查看部署信息和清单。  它应类似于上述部署清单。  选择“提交”****。

12. 将模块部署到设备之后，即可在门户的“设备详细信息”页中查看所有模块****。 此页面显示每个已部署模块的名称，以及部署状态和退出代码等有用信息。

## <a name="deploying-using-azure-cli"></a>使用 Azure CLI 进行部署

### <a name="prerequisites"></a>先决条件

1. [从这里](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)安装最新版本的[Azure 命令行接口 （AZ）。](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

### <a name="quickstart"></a>快速入门

1. 将上述部署清单保存到文件中`deployment.json`。  

2. 使用以下命令将配置应用于 IoT Edge 设备：

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   参数`device id`区分大小写。 content 参数指向你保存的部署清单文件。 
    ![az IoT 边缘设置模块输出](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. 将模块部署到设备后，可以使用以下命令查看所有模块：

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   设备 ID 参数区分大小写。 ![az iot hub module-identity list output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>后续步骤

现在，您已经了解如何从头开始部署 OPC Twin，下面是建议的下一步：

> [!div class="nextstepaction"]
> [将 OPC 孪生部署到现有项目](howto-opc-twin-deploy-existing.md)
