---
title: 将 Azure IoT Edge 设备添加到 Azure IoT Central | Microsoft Docs
description: 以操作员身份将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: bf74784998de6bbad6310c48c24d6353512bff13
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026390"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>教程：将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序

本教程介绍如何配置 Azure IoT Edge 设备并将其添加到 Azure IoT Central 应用程序。 本教程使用 Azure 市场中提供的支持 IoT Edge 的 Linux 虚拟机 (VM) 来模拟 IoT Edge 设备。 该 IoT Edge 设备使用一个可以生成模拟环境遥测数据的模块。 你将在 IoT Central 应用程序中的仪表板上查看遥测数据。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 为 IoT Edge 设备创建设备模板
> * 在 IoT Central 中创建 IoT Edge 设备
> * 将模拟的 IoT Edge 设备部署到 Linux VM

## <a name="prerequisites"></a>必备条件

完成[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)快速入门，使用“自定义应用”>“自定义应用程序”模板创建 IoT Central 应用程序。 

需要有效的 Azure 订阅才能完成本教程中的步骤。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

从 GitHub 下载 IoT Edge 清单文件。 右键单击以下链接，然后选择“将链接另存为”：  [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>创建设备模板

在本部分，你将为要连接到 IoT Central 应用程序的 IoT Edge 设备创建一个设备模板。 导入 IoT Edge 清单以开始操作，然后修改模板以添加遥测定义和视图：

### <a name="import-manifest-to-create-template"></a>导入清单以创建模板

若要从 IoT Edge 清单创建设备模板：

1. 在 IoT Central 应用程序中，导航到“设备模板”并选择“+ 新建”。  

1. 在“选择模板类型”页上，选择“Azure IoT Edge”磁贴。   然后选择“下一步:  自定义”。

1. 在“上传 Azure IoT Edge 部署清单”页上，选择“浏览”上传前面下载的 **EnvironmentalSensorManifest.json**。   然后选择“下一步:  查看”。

1. 在“查看”页上，选择“创建”   。

1. 创建模板后，将其名称更改为“环境传感器边缘设备”。 

1. 在“SimulatedTemperatureSensor”模块中选择“管理”界面，以查看清单中定义的两个属性：  

![从 IoT Edge 清单创建的设备模板](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>将遥测功能添加到清单

IoT Edge 清单不会定义模块发送的遥测数据。 必须将遥测定义添加到设备模板。 **SimulatedTemperatureSensor** 模块发送类似于以下 JSON 的遥测消息：

```json
{
    "machine": {
        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

若要将遥测定义添加到设备模板：

1. 在“环境传感器边缘设备”模板中选择“管理”界面。  

1. 选择“+ 添加功能”。  输入 *machine* 作为**显示名称**，并确保“功能类型”为“遥测”。  

1. 选择“对象”作为架构类型，然后选择“定义”。   在对象定义页上，添加 *temperature* 和 *pressure* 作为 **Double** 类型的特征，然后选择“应用”。 

1. 选择“+ 添加功能”。  输入 *ambient* 作为**显示名称**，并确保“功能类型”为“遥测”。  

1. 选择“对象”作为架构类型，然后选择“定义”。   在对象定义页上，添加 *temperature* 和 *humidity* 作为 **Double** 类型的特征，然后选择“应用”。 

1. 选择“+ 添加功能”。  输入 *timeCreated* 作为**显示名称**，并确保“功能类型”为“遥测”。  

1. 选择“日期时间”作为架构类型。 

1. 选择“保存”以更新模板。 

“管理”界面现在包含 **machine**、**ambient** 和 **timeCreated** 遥测类型： 

![包含 machine 和 ambient 遥测类型的界面](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>将视图添加到模板

设备模板暂时还不提供视图来让操作员查看 IoT Edge 设备发出的遥测数据。 若要将视图添加到设备模板：

1. 在“环境传感器边缘设备”模板中选择“视图”。  

1. 在“选择添加新视图”页上，选择“可视化设备”磁贴。  

1. 将视图名称更改为“查看 IoT Edge 设备遥测数据”。 

1. 选择 **ambient** 和 **machine** 遥测类型。 然后选择“添加磁贴”。 

1. 选择“保存”以保存“查看 IoT Edge 设备遥测数据”视图。  

![具有遥测视图的设备模板](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>发布模板

在添加使用“环境传感器边缘设备”模板的设备之前，必须发布该模板。 

导航到“环境传感器边缘设备”模板并选择“发布”。   然后选择“发布”以发布该模板： 

![发布设备模板](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>添加 IoT Edge 设备

现已发布“环境传感器边缘设备”模板，接下来可将设备添加到 IoT Central 应用程序： 

1. 在 IoT Central 应用程序中导航到“设备”页，并在可用模板列表中选择“环境传感器边缘设备”。  

1. 选择 **+** 以从模板添加新设备。 在“创建新设备”页上，选择“创建”。  

现在，已有一个状态为“已注册”的新设备： 

![发布设备模板](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>获取设备凭据

稍后在本教程中部署 IoT Edge 设备时，需要提供凭据，使设备能够连接到 IoT Central 应用程序。 若要获取设备凭据：

1. 在“设备”页上，选择创建的设备。 

1. 选择“连接”  。

1. 在“设备连接”页上，记下“ID 范围”、“设备 ID”和“主密钥”。     稍后要使用这些值。

1. 选择“关闭”  。

现已完成 IoT Central 应用程序的配置，使 IoT Edge 设备能够建立连接。

## <a name="deploy-an-iot-edge-device"></a>部署 IoT Edge 设备

在本教程中，你将使用 Azure 上创建的支持 Azure IoT Edge 的 Linux VM 来模拟 IoT Edge 设备。 创建支持 IoT Edge 的 VM：

1. 在 Azure 市场中导航到 [Ubuntu 上的 Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)。 然后选择“立即获取”。 

1. 在“在 Azure 中创建此应用”页上，选择“继续”。   此链接会将你转到 Azure 门户，在其中你可能需要登录到自己的 Azure 订阅。

1. 在 Azure 门户中的“Ubuntu 上的 Azure IoT Edge”页上，选择“创建”。  

1. 在“创建虚拟机”>“基本信息”页上： 

    - 选择 Azure 订阅。
    - 创建名为 **iot-edge-devices** 的新资源组。
    - 使用虚拟机名称：**iotedgevm**。
    - 选择离你最近的区域。
    - 将身份验证类型设置为“密码”。 
    - 选择用户名和密码。
    - 可将其他选项保留默认值。
    - 选择“查看 + 创建”  。

1. 验证完成后，选择“创建”。 

几分钟后当部署完成时，请选择“转到资源”。 

### <a name="provision-vm-as-an-iot-edge-device"></a>将 VM 预配为 IoT Edge 设备 

若要将 VM 预配为 IoT Edge 设备：

1. 在“支持 + 故障排除”部分，选择“串行控制台”。  

1. 按 **Enter** 显示 `login:` 提示符。 输入用户名和密码以登录。

1. 运行以下命令检查 IoT Edge 运行时版本。 撰写本文时，版本为 1.0.8：

    ```bash
    sudo iotedge --version
    ```

1. 使用 `nano` 编辑器打开 IoT Edge config.yaml 文件：

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. 向下滚动，直到看到 `# Manual provisioning configuration`。 注释掉接下来的三行，如以下代码片段中所示：

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. 向下滚动，直到看到 `# DPS symmetric key provisioning configuration`。 取消注释接下来的八行，如以下代码片段中所示：

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. 将 `{scope_id}` 替换为前面记下的“ID 范围”。 

1. 将 `{registration_id}` 替换为前面记下的“设备 ID”。 

1. 将 `{symmetric_key}` 替换为前面记下的“主密钥”。 

1. 保存更改 (**Ctrl-O**) 并退出 (**Ctrl-X**) `nano` 编辑器。

1. 运行以下命令重启 IoT Edge 守护程序：

    ```bash
    sudo systemctl restart iotedge
    ```

1. 若要检查 IoT Edge 模块的状态，请运行以下命令：

    ```bash
    iotedge list
    ```

    输出如下所示：

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>查看遥测

模拟的 IoT Edge 设备现在正在 VM 中运行。 在 IoT Central 应用程序中，“设备”页上的设备状态现在为“已预配”：  

![已预配设备](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

可以在“查看 IoT Edge 设备遥测数据”页上查看遥测数据： 

![设备遥测](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

“模块”页显示 IoT Edge 模块的状态： 

![设备遥测](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>后续步骤

现在你已了解如何在 IoT Central 中使用和管理 IoT Edge 设备，建议接下来执行以下步骤：

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [配置透明网关](../../iot-edge/how-to-create-transparent-gateway.md)
