---
title: 教程 - 在 Azure IoT Central 中创建视频分析 - 对象和运动检测应用程序
description: 本教程演示如何在 IoT Central 中创建视频分析应用程序。 你将创建该应用程序，对其进行自定义，然后将其连接到其他 Azure 服务。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: b98406984f2c9f2adfca030369a6ea3d47a786f3
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762771"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>教程：在 Azure IoT Central 中创建视频分析 - 对象和运动检测应用程序

以解决方案构建者的身份，了解如何使用 IoT Central 视频分析 - 对象和运动检测应用程序模板、Azure IoT Edge 设备和 Azure 媒体服务创建视频分析应用程序。 该解决方案使用零售商店来说明如何满足监视监控摄像头的常见业务需求， 并在视频源中使用自动对象检测来快速识别和查找感兴趣的事件。

该示例应用程序包括两个模拟设备和一个 IoT Edge 网关。 以下教程介绍了两种方法来试验和了解网关的功能：

* 在 Azure VM 中创建 IoT Edge 网关，并连接模拟相机。
* 在真实设备（如 Intel NUC）上创建 IoT Edge 网关，并连接到真实相机。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 使用 Azure IoT Central 视频分析应用程序模板来创建零售商店应用程序
> * 自定义应用程序设置
> * 为 IoT Edge 网关设备创建设备模板
> * 将网关设备添加到 IoT Central 应用程序

## <a name="prerequisites"></a>先决条件

若要完成本教程系列，需要：

* Azure 订阅。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上创建一个。
* 如果使用的是真实相机，则需要在 IoT Edge 设备和相机之间建立连接，并且需要“实时流式处理协议”通道。

## <a name="initial-setup"></a>初始设置

在这些教程中，你将更新并使用多个配置文件。 这些文件的初始版本在 [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub 存储库中提供。 该存储库还包括一个 scratchpad 文本文件，你可下载该文件并将其用于记录部署的服务中的配置值。

在本地计算机上创建一个名为 lva-configuration 的文件夹以保存这些文件的副本。 然后右键单击以下每个链接，并选择“另存为”将文件保存到 lva-configuration 文件夹中：

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> GitHub 存储库还包括 LvaEdgeGatewayModule 和 lvaYolov3 IoT Edge 模块的源代码 。 有关使用源代码的详细信息，请参阅[生成 LVA Gateway 模块](tutorial-video-analytics-build-module.md)。

## <a name="deploy-and-configure-azure-media-services"></a>部署和配置 Azure 媒体服务

该解决方案使用 Azure 媒体服务帐户来存储 IoT Edge 网关设备发出的对象检测视频剪辑。

创建媒体服务帐户时：

- 需要提供帐户名、Azure 订阅、位置、资源组和存储帐户。 创建媒体服务帐户时，请使用默认设置创建新的存储帐户。

- 对于位置，请选择“美国东部”区域。

- 在“美国东部”区域中，为媒体服务和存储帐户创建名为 lva-rg 的新资源组。 完成教程后，通过删除 lva-rg 资源组可以轻松删除所有资源。

[在 Azure 门户中创建媒体服务帐户](https://portal.azure.com/?r=1#create/Microsoft.MediaService)。

> [!TIP]
> 这些教程在所有示例中都使用“美国东部”区域。 如果需要，你可以使用离你最近的区域。

在 scratchpad.txt 文件中记下媒体服务帐户名称。

部署完成后，导航到媒体服务帐户的“属性”页 。 在 scratchpad.txt 文件中记下资源 ID，稍后在配置 IoT Edge 模块时要使用此值。

接下来，为媒体服务资源配置 Azure Active Directory 服务主体。 依次选择“API 访问”和“服务主体身份验证” 。 创建一个新的 Azure Active Directory 应用，名称与媒体服务资源相同，然后创建一个机密并附加 IoT Edge 访问权限说明。

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="为 AMS 配置 AAD 应用":::

生成机密后，向下滚动到“复制凭据以连接服务主体应用程序”部分。 然后选择“JSON”。 你可以从此处一次性复制所有凭据信息。 在 scratchpad.txt 文件中记下此信息，稍后在配置 IoT Edge 设备时要使用此信息。

> [!WARNING]
> 这是你查看并保存机密的唯一机会。 如果丢失了，则必须生成另一个机密。

## <a name="create-the-azure-iot-central-application"></a>创建 Azure IoT Central 应用程序

在本部分中，将从模板创建新的 Azure IoT Central 应用程序。 你将在本系列教程中使用此应用程序来生成完整的解决方案。

若要创建新的 Azure IoT Central 应用程序：

1. 导航到 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站。

1. 使用用于访问 Azure 订阅的凭据登录。

1. 若要开始创建新的 Azure IoT Central 应用程序，请在“生成”页上选择“新建应用程序” 。

1. 选择“零售”  。 “零售”页将显示多个零售应用程序模板。

若要创建新的视频分析应用程序，请执行以下操作：

1. 选择“视频分析 - 对象和运动检测”应用程序模板。 此模板包括本教程中使用的设备的设备模板， 模板包括运算符可用于执行任务（如监视和管理相机）的示例仪表板。

1. （可选）选择一个友好的应用程序名称  。 此应用程序基于名为 Northwind Traders 的虚构零售商店。 本教程使用 Northwind Traders 视频分析作为应用程序名称。

    > [!NOTE]
    > 如果使用易记的应用程序名称，仍必须对应用程序 URL 使用唯一的值 。

1. 如果你有一个 Azure 订阅，请选择“目录”、“Azure 订阅”并选择“美国”作为“位置”   。 如果你没有订阅，则可以启用 7 天免费试用版并填写所需的联系人信息  。 由于本教程使用三个设备（两个相机、一个 IoT Edge 设备），如果你不使用免费试用版，将按使用量计费。

    有关目录、订阅和位置的详细信息，请参阅[创建应用程序快速入门](../core/quick-deploy-iot-central.md)。

1. 选择“创建”。

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Azure IoT Central 的“创建应用程序”页":::

### <a name="retrieve-the-configuration-data"></a>检索配置数据

稍后在本教程中配置 IoT Edge 网关时，需要 IoT Central 应用程序中的某些配置数据。 IoT Edge 设备需要此信息在应用程序中注册并连接到该应用程序。

在“管理”部分中，选择“你的应用程序”并在 scratchpad.txt 文件中记下应用程序 URL 和应用程序 ID   ：

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="管理":::

选择“API 令牌”，并为“操作员”角色生成名为 LVAEdgeToken 的新令牌  ：

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="生成令牌":::

在 scratchpad.txt 文件中记下令牌供稍后使用。 对话框关闭后，不能再次查看令牌。

在“管理”部分中，选择“设备连接”，然后选择“SAS-IoT-设备”  。

在 scratchpad.txt 文件中记下设备的“主密钥”。 稍后在配置 IoT Edge 设备时，你将使用此“主要组共享访问签名令牌”。

## <a name="edit-the-deployment-manifest"></a>编辑部署清单

使用部署清单部署 IoT Edge 模块。 在 IoT Central 中，可以将清单作为设备模板导入，然后让 IoT Central 管理模块部署。

若要准备部署清单，请执行以下操作：

1. 使用文本编辑器打开保存在 lva-configuration 文件夹中的 deployment.amd64.json 文件 。

1. 找到 `LvaEdgeGatewayModule` 设置并更改映像名称，如以下代码片段所示：

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. 在 `LvaEdgeGatewayModule` 部分的 `env` 节点中添加媒体服务帐户的名称。 你已在 cratchpad.txt 文件中记下该帐户名称：

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. 该模板不会在 IoT Central 中公开这些属性，因此需要将媒体服务配置值添加到部署清单。 找到 `lvaEdge` 模块，并将占位符替换为在创建媒体服务帐户时在 scratchpad.txt 文件中记下的值。

    `azureMediaServicesArmId` 是在创建媒体服务帐户时在 scratchpad.txt 文件中记下的资源 ID。

    你已在创建媒体服务帐户的服务主体时在 scratchpad.txt 文件中记下 `aadTenantId`、`aadServicePrincipalAppId` 和 `aadServicePrincipalSecret`：

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. 保存更改。

还可以选择从 Intel 将 Yolov3 模块替换为已优化硬件的 [OpenVINO&trade; 模型服务器，即 Edge AI 扩展模块](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper)。 可以下载示例部署清单 [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)。 此清单会将 `lvaYolov3` 模块的配置设置替换为以下配置：

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>创建 Azure IoT Edge 网关设备

视频分析 - 对象和运动检测应用程序包括“LVA Edge 对象探测器”设备模板和“LVA Edge 运动检测”设备模板 。 在本部分中，你将使用部署清单创建网关设备模板，并将网关设备添加到 IoT Central 应用程序。

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>为 LVA Edge 网关创建设备模板

若要导入部署清单并创建“LVA Edge 网关”设备模板，请执行以下操作：

1. 在 IoT Central 应用程序中，导航到“设备模板”并选择“+ 新建” 。

1. 在“选择模板类型”页上，选择“Azure IoT Edge”磁贴。  然后选择“下一步:自定义”。

1. 在“上传 Azure IoT Edge 部署清单”页上，输入“LVA Edge 网关”作为模板名称，并选中“具有下游设备的网关设备”。

    暂时不要浏览部署清单。 如果浏览了部署清单，则部署向导需要为每个模块提供一个接口，但你只需要公开 LvaEdgeGatewayModule 的接口。 在后面的步骤中上传清单。

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="请勿上传部署清单":::

    在完成时选择“下一步:  查看”。

1. 在“查看”页上，选择“创建”   。

### <a name="import-the-device-capability-model"></a>导入设备功能模型

设备模板必须包含设备功能模型。 在“LVA Edge 网关”页上，选择“导入功能模型”磁贴 。 导航到之前创建的 lva-configuration 文件夹，并选择 LvaEdgeGatewayDcm.json 文件 。

“LVA Edge 网关”设备模板现在包含“LVA Edge 网关模块”以及三个接口 ：设备信息、LVA Edge 网关设置和 LVA Edge 网关接口  。

### <a name="replace-the-manifest"></a>替换清单

在“LVA Edge 网关”页上，选择“+ 替换清单” 。

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="替换清单":::

导航到 lva-configuration 文件夹，然后选择之前编辑的 deployment.amd64.json 。 选择“上传”。 验证完成后，选择“替换”。

### <a name="add-relationships"></a>添加关系

在“LVA Edge 网关”设备模板中的“模块/LVA Edge 网关模块”下，选择“关系”  。 选择“+ 添加关系”，然后添加以下两个关系：

|显示名称               |名称          |目标 |
|-------------------------- |------------- |------ |
|LVA Edge 运动探测器   |使用默认值   |LVA Edge 运动探测器设备 |
|LVA Edge 对象探测器   |使用默认值   |LVA Edge 对象探测器设备 |

再选择“保存”。

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="添加关系":::

### <a name="add-views"></a>添加视图

“LVA Edge 网关”设备模板不包含任何视图定义。

若要将视图添加到设备模板：

1. 在“LVA Edge 网关”设备模板中，导航到“视图”并选择“可视化设备”磁贴  。

1. 输入“LVA Edge 网关设备”作为视图名称。

1. 将以下磁贴添加到视图：

    * 具有以下设备信息属性的磁贴：设备型号、制造商、操作系统、处理器体系结构、软件版本、总内存和总存储      。
    * 具有“可用内存”和“系统检测信号”遥测值的折线图磁贴 。
    * 具有以下事件的事件历史记录磁贴：创建相机、删除相机、模块重启、模块已启动、模块已停止    。
    * 显示“IoT Central 客户端状态”遥测的 2x1 上一个已知值磁贴。
    * 显示“模块状态”遥测的 2x1 上一个已知值磁贴。
    * 显示“系统检测信号”遥测的 1x1 上一个已知值磁贴。
    * 显示“已连接相机”遥测的 1x1 上一个已知值磁贴。

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="仪表板":::

1. 选择“保存” 。

### <a name="publish-the-device-template"></a>发布设备模板

在将设备添加到应用程序之前，必须先发布设备模板：

1. 在“LVA Edge 网关”设备模板中选择“发布” 。

1. 在“将此设备模板发布到应用程序”页中，选择“发布” 。

LVA Edge 网关现在可作为设备类型在应用程序的“设备”页上使用。

## <a name="add-a-gateway-device"></a>添加网关设备

若要向应用程序添加“LVA Edge 网关”设备，请执行以下操作：

1. 导航到“设备”页，然后选择“LVA Edge 网关”设备模板 。

1. 选择“+ 新建”。

1. 在“创建新设备”对话框中，将设备名称更改为 LVA Gateway 001，并将设备 ID 改为 lva-gateway-001** 。

    > [!NOTE]
    > 设备 ID 在应用程序中必须是唯一的。

1. 选择“创建”。

设备状态为“已注册”。

### <a name="get-the-device-credentials"></a>获取设备凭据

要使设备能够连接到 IoT Central 应用程序，则需要提供凭据。 若要获取设备凭据：

1. 在“设备”页上，选择创建的 lva-gateway-001 设备 。

1. 选择“连接”。

1. 在“设备连接”页上，在 scratchpad.txt 文件中记下 ID 范围、设备 ID 和设备主密钥  。 稍后要使用这些值。

1. 请确保将连接方法设置为“共享访问签名”。

1. 选择“关闭”。

## <a name="next-steps"></a>后续步骤

现在你已使用“视频分析 - 对象和运动检测”应用程序模板创建了一个 IoT Central 应用程序，为网关设备创建了一个设备模板，并向应用程序中添加了一个网关设备。

如果要使用运行云 VM 的 IoT Edge 模块和模拟视频流试用视频分析 - 对象和运动检测应用程序，请：

> [!div class="nextstepaction"]
> [创建用于视频分析的 IoT Edge 实例 (Linux VM)](./tutorial-video-analytics-iot-edge-vm.md)

如果要使用运行真实设备的 IoT Edge 模块和真实 ONVIF 相机试用视频分析 - 对象和运动检测应用程序，请：

> [!div class="nextstepaction"]
> [创建用于视频分析的 IoT Edge 实例 (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
