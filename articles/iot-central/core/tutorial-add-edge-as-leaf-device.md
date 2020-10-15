---
title: 教程 - 将 Azure IoT Edge 设备添加到 Azure IoT Central | Microsoft Docs
description: 教程 - 以操作员身份将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 9b4bb462c94ab5a59dbd9d8fdd4cf619e311df56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987017"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>教程：将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序

本文适用于操作员、解决方案构建者和设备开发人员。

本教程介绍如何配置 Azure IoT Edge 设备并将其添加到 Azure IoT Central 应用程序。 本教程使用支持 IoT Edge 的 Linux 虚拟机 (VM) 来模拟 IoT Edge 设备。 该 IoT Edge 设备使用一个可以生成模拟环境遥测数据的模块。 你将在 IoT Central 应用程序中的仪表板上查看遥测数据。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 为 IoT Edge 设备创建设备模板
> * 在 IoT Central 中创建 IoT Edge 设备
> * 将模拟的 IoT Edge 设备部署到 Linux VM

## <a name="prerequisites"></a>先决条件

完成[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)快速入门，使用“自定义应用”>“自定义应用程序”模板创建 IoT Central 应用程序。

需要有效的 Azure 订阅才能完成本教程中的步骤。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

从 GitHub 下载 IoT Edge 清单文件。 右键单击以下链接，然后选择“将链接另存为”：[EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>创建设备模板

在本部分，你将为 IoT Edge 设备创建 IoT Central 设备模板。 导入 IoT Edge 清单以开始操作，然后修改模板以添加遥测定义和视图：

### <a name="import-manifest-to-create-template"></a>导入清单以创建模板

若要从 IoT Edge 清单创建设备模板：

1. 在 IoT Central 应用程序中，导航到“设备模板”并选择“+ 新建”。 

1. 在“选择模板类型”页上，选择“Azure IoT Edge”磁贴。  然后选择“下一步:自定义”。

1. 在“上传 Azure IoT Edge 部署清单”页上，输入“环境传感器边缘设备”作为设备模板名称。 然后选择“浏览”上传前面下载的 EnvironmentalSensorManifest.json 。 然后选择“下一步:查看”。

1. 在“查看”页上，选择“创建” 。

1. 在“SimulatedTemperatureSensor”模块中选择“管理”界面，以查看清单中定义的两个属性： 

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="从 IoT Edge 清单创建的设备模板":::

### <a name="add-telemetry-to-manifest"></a>将遥测功能添加到清单

IoT Edge 清单不会定义模块发送的遥测数据。 将遥测定义添加到 IoT Central 中的设备模板。 **SimulatedTemperatureSensor** 模块发送类似于以下 JSON 的遥测消息：

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

1. 选择“+ 添加功能”。 输入 *machine* 作为**显示名称**，并确保“功能类型”为“遥测”。 

1. 选择“对象”作为架构类型，然后选择“定义”。  在对象定义页上，添加 *temperature* 和 *pressure* 作为 **Double** 类型的特征，然后选择“应用”。

1. 选择“+ 添加功能”。 输入 *ambient* 作为**显示名称**，并确保“功能类型”为“遥测”。 

1. 选择“对象”作为架构类型，然后选择“定义”。  在对象定义页上，添加 *temperature* 和 *humidity* 作为 **Double** 类型的特征，然后选择“应用”。

1. 选择“+ 添加功能”。 输入 *timeCreated* 作为**显示名称**，并确保“功能类型”为“遥测”。 

1. 选择“日期时间”作为架构类型。

1. 选择“保存”以更新模板。

“管理”界面现在包含 **machine**、**ambient** 和 **timeCreated** 遥测类型：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="从 IoT Edge 清单创建的设备模板":::

### <a name="add-views-to-template"></a>将视图添加到模板

设备模板暂时还不提供视图来让操作员查看 IoT Edge 设备发出的遥测数据。 若要将视图添加到设备模板：

1. 在“环境传感器边缘设备”模板中选择“视图”。 

1. 在“选择添加新视图”页上，选择“可视化设备”磁贴。 

1. 将视图名称更改为“查看 IoT Edge 设备遥测数据”。

1. 选择 **ambient** 和 **machine** 遥测类型。 然后选择“添加磁贴”。

1. 选择“保存”以保存“查看 IoT Edge 设备遥测数据”视图。 

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="从 IoT Edge 清单创建的设备模板":::

### <a name="publish-the-template"></a>发布模板

在添加使用“环境传感器边缘设备”模板的设备之前，必须发布该模板。

导航到“环境传感器边缘设备”模板并选择“发布”。  在“将此设备模板发布到应用程序”面板中，选择“发布”以发布模板 ：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="从 IoT Edge 清单创建的设备模板":::

## <a name="add-iot-edge-device"></a>添加 IoT Edge 设备

现已发布“环境传感器边缘设备”模板，接下来可将设备添加到 IoT Central 应用程序：

1. 在 IoT Central 应用程序中导航到“设备”页，并在可用模板列表中选择“环境传感器边缘设备”。 

1. 选择“+ 新建”以从模板添加新设备。 在“创建新设备”页上，选择“创建”。 

现在，已有一个状态为“已注册”的新设备：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="从 IoT Edge 清单创建的设备模板":::

### <a name="get-the-device-credentials"></a>获取设备凭据

稍后在本教程中部署 IoT Edge 设备时，需要提供凭据，使设备能够连接到 IoT Central 应用程序。 若要获取设备凭据：

1. 在“设备”页上，选择创建的设备。

1. 选择“连接”。

1. 在“设备连接”页上，记下“ID 范围”、“设备 ID”和“主密钥”。    稍后要使用这些值。

1. 选择“关闭”。

现已完成 IoT Central 应用程序的配置，使 IoT Edge 设备能够建立连接。

## <a name="deploy-an-iot-edge-device"></a>部署 IoT Edge 设备

在本教程中，你将使用 Azure 上创建的支持 Azure IoT Edge 的 Linux VM 来模拟 IoT Edge 设备。 要在 Azure 订阅中创建支持 IoT Edge 的 VM，请单击：

[![用于 iotedge-vm-deploy 的“部署到 Azure”按钮](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

在“自定义部署”页上：

1. 选择 Azure 订阅。

1. 选择“新建”以创建名为 central-edge-rg 的新资源组。

1. 选择离你较近的区域。

1. 添加唯一的 DNS 标签前缀，例如 contoso-central-edge。

1. 选择虚拟机的管理员用户名。

1. 输入“temp”作为连接字符串。 稍后，将设备配置为使用 DPS 连接。

1. 接受 VM 大小、Ubuntu 版本和位置的默认值。

1. 选择“密码”作为身份验证类型。

1. 输入 VM 的密码。

1. 然后选择“查看 + 创建”。

1. 查看你的选择，然后选择“创建”：

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="从 IoT Edge 清单创建的设备模板":::

部署需要几分钟时间完成。 部署完成后，导航到 Azure 门户中的 central-edge-rg 资源组。

### <a name="configure-the-iot-edge-vm"></a>配置 IoT Edge VM

要将 VM 中的 IoT Edge 配置为使用 DPS 注册并连接到 IoT Central 应用程序，请执行以下操作：

1. 在 contoso-edge-rg 资源组中，选择虚拟机实例。

1. 在“支持 + 故障排除”部分，选择“串行控制台”。  如果系统提示你配置启动诊断，请按照门户中的说明进行操作。

1. 按 **Enter** 显示 `login:` 提示符。 输入用户名和密码以登录。

1. 运行以下命令检查 IoT Edge 运行时版本。 撰写本文时，版本为 1.0.9.1：

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
    #  device_connection_string: "temp"
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

    > [!TIP]
    > 请确保 `provisioning:` 前面没有空格

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

    以下示例输出显示正在运行的模块：

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > 可能需要等待所有模块开始运行。

## <a name="view-the-telemetry"></a>查看遥测

模拟的 IoT Edge 设备现在正在 VM 中运行。 在 IoT Central 应用程序中，“设备”页上的设备状态现在为“已预配”： 

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="从 IoT Edge 清单创建的设备模板":::

可以在“查看 IoT Edge 设备遥测”页上查看该设备的遥测：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="从 IoT Edge 清单创建的设备模板":::

“模块”页显示设备上的 IoT Edge 模块的状态：

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="从 IoT Edge 清单创建的设备模板":::

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用 IoT Edge VM，则可以保留在本教程中使用的资源，然后重复使用。 否则，可删除在本教程中创建的资源，以免产生额外的费用：

* 要删除 IoT Edge VM 及其关联的资源，请删除 Azure 门户中的 contoso-edge-rg 资源组。
* 要删除 IoT Central 应用程序，请导航至该应用程序“管理”部分中的“你的应用程序”页面，然后选择“删除”  。

既然解决方案开发人员或操作员已了解了如何在 IoT Central 中使用和管理 IoT Edge 设备，建议执行的下一步操作是：

> [!div class="nextstepaction"]
> [使用设备组分析设备遥测](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>后续步骤

作为设备开发人员，现在你已了解了如何在 IoT Central 中使用和管理 IoT Edge 设备，建议执行的下一步操作是阅读：

> [!div class="nextstepaction"]
> [开发 IoT Edge 模块](../../iot-edge/tutorial-develop-for-linux.md)