---
title: 部署空间分析 web 应用
titleSuffix: Azure Cognitive Services
description: 了解如何在 web 应用程序中使用空间分析。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: aahi
ms.openlocfilehash: 8032c3607dd74cddbaa5fd6690a95ebdf218809a
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628188"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>如何：部署计算 web 应用程序的人员

本文介绍如何将空间分析集成到可了解人员移动的 web 应用中，并监视占用物理空间的人员数。 

在本教程中，将了解如何：

* 部署空间分析容器
* 配置操作和照相机
* 在 Web 应用程序中配置 IoT 中心连接
* 部署并测试 Web 应用程序

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/cognitive-services/)
* 基本了解 Azure IoT Edge 部署配置和 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)
* 已配置的 [主计算机](spatial-analysis-container.md)。

## <a name="deploy-the-spatial-analysis-container"></a>部署空间分析容器

填写 [请求应用程序](https://aka.ms/csgate) 以获取运行容器的访问权限。 

按照 [主机计算机设置](./spatial-analysis-container.md) 来配置主机计算机并将 IoT Edge 设备连接到 Azure IoT 中心。 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>在订阅中部署 Azure IoT 中心服务

首先，使用标准定价层 (S1) 或免费层 (S0) 创建 Azure IoT 中心服务的实例。 按照这些说明使用 Azure CLI 创建此实例。

填写所需的参数：
* 订阅： Azure 订阅的名称或 ID
* 资源组：创建资源组的名称
* Iot 中心名称：为 IoT 中心创建名称
* IoTHub Name：创建的 IoT 中心的名称 
* 边缘设备名称：创建边缘设备的名称

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>在主计算机上的 Azure IoT Edge 上部署容器

使用 Azure CLI 将空间分析容器作为 IoT 模块部署在主计算机上。 部署过程需要部署清单文件，其中概述了部署所需的容器、变量和配置。 可在 GitHub 上找到示例 [部署清单](https://github.com/Azure-Samples/cognitive-services-rest-api-samples/) ，其中包括 *空间分析* 容器的基本部署配置。 

> [!NOTE] 
> *空间分析-telegraf*和*空间分析-诊断*容器是可选的。 您可以决定将其从 *DeploymentManifest.js* 的文件中删除。 有关详细信息，请参阅 [遥测和故障排除](./spatial-analysis-logging.md) 一文。 可在[Github](https://go.microsoft.com/fwlink/?linkid=2142179)上找到*有关*文件的示例DeploymentManifest.js 

### <a name="set-environment-variables"></a>设置环境变量

在上面链接的文件的示例*DeploymentManifest.js*中已设置了 IoT Edge 模块的大部分**环境变量**。 在文件中，搜索 `BILLING_ENDPOINT` 和 `API_KEY` 环境变量，如下所示。 将值替换为前面创建的终结点 URI 和 API 密钥。 确保 EULA 值设置为 "接受"。 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>配置操作参数

完成 *空间分析* 容器的初始配置后，下一步是配置操作参数，并将其添加到部署中。 

第一步是更新示例 [部署清单](https://go.microsoft.com/fwlink/?linkid=2142179) 并为其配置 operationId， `cognitiveservices.vision.spatialanalysis-personcount` 如下所示：


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

更新 [部署清单](https://go.microsoft.com/fwlink/?linkid=2142179) 后，请按照照相机制造商的说明安装照相机、配置照相机 url，并配置用户名和密码。 

接下来，将设置 `VIDEO_URL` 为照相机的 RTSP url，以及用于连接到相机的凭据。

如果边缘设备具有多个 GPU，请选择要对其运行此操作的 GPU。 请确保对在单个 GPU 上运行的操作不超过8个的操作进行负载均衡。  

接下来，配置要对其进行计数的区域。 若要配置区域多边形，请首先按照制造商的说明从照相机检索帧。 若要确定多边形的每个顶点，请在帧上选择一个点，获取该点相对于该框架左边缘、上角的 x，y 像素坐标，然后再除以相应的帧尺寸。 将结果设置为顶点的 x 和 y 坐标。 您可以在字段中设置区域多边形配置 `SPACEANALYTICS_CONFIG` 。

这是一个示例视频帧，显示了如何为大小为1920/1080 的帧计算顶点坐标。
![示例视频帧](./media/spatial-analysis/sample-video-frame.jpg)

你还可以选择在检测到的人员计数并生成事件时的置信度阈值。 如果希望输出所有事件，请将阈值设置为0。

### <a name="execute-the-deployment"></a>执行部署

[部署清单](https://go.microsoft.com/fwlink/?linkid=2142179)完成后，请在 Azure CLI 中使用此命令，将主计算机上的容器作为 IoT Edge 模块部署。

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

填写所需的参数：

* IoT 中心名称： Azure IoT 中心名称
* DeploymentManifest.js：部署文件的名称
* IoT Edge 设备名称：主计算机的 IoT Edge 设备名称
* 订阅：你的订阅 ID 或名称

此命令将开始部署，你可以在 Azure 门户的 Azure IoT 中心实例中查看部署状态。 状态可能显示为 *417 –设备的部署配置* 在设备完成下载容器映像并开始运行之前未设置。

### <a name="validate-that-the-deployment-was-successful"></a>验证部署是否成功

在 Azure 门户的 IoT 中心实例中的空间分析模块 IoT Edge 模块设置中找到 " *运行时状态* "。 *运行时状态*的**所需值**和**报告的值**应为 `Running` 。 请参阅下面的内容，了解 Azure 门户上的外观。

![部署验证示例](./media/spatial-analysis/deployment-verification.png)

此时，空间分析容器正在运行操作。 它为操作发出 AI insights `cognitiveservices.vision.spatialanalysis-personcount` ，并将这些见解作为遥测路由到 Azure IoT 中心实例。 若要配置其他相机，可以更新 [部署清单](https://go.microsoft.com/fwlink/?linkid=2142179) 文件并再次执行部署。

## <a name="person-counting-web-application"></a>计算 Web 应用程序的人员

使用此人员计数 web 应用程序，可以快速配置示例 web 应用并将其托管在 Azure 环境中。

### <a name="get-the-person-counting-app-container"></a>获取用户计数应用容器

此应用的容器窗体在 Azure 容器注册表中提供。 使用以下 docker pull 命令下载它。 请与 Microsoft 联系 projectarchon@microsoft.com 以获取访问令牌。

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

将容器推送到 Azure 容器注册表 (ACR) 。

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

若要安装该容器，请创建新的 Azure 用于容器的 Web 应用并填写所需的参数。 然后，**依次单击 "** **Docker** " 选项卡和 " **Azure 容器注册表**"。 使用你在其中推送了映像的 Azure 容器注册表实例。

![输入图像详细信息](./media/spatial-analysis/solution-app-create-screen.png)

输入以上参数后，单击 " **查看" + "创建** 并创建应用"。

### <a name="configure-the-app"></a>配置应用 

等待安装完成，然后导航到 Azure 门户中的资源。 请在 " **配置** " 部分中，添加以下两个 **应用程序设置**。

* `EventHubConsumerGroup` –使用者组在你的 Azure IoT 中心的字符串名称，你可以在 IoT 中心创建新的使用者组，或使用默认组。 
* `IotHubConnectionString`– Azure IoT 中心的连接字符串，可从 Azure IoT 中心资源配置参数的密钥部分检索 ![](./media/spatial-analysis/solution-app-config-page.png)

添加这两个设置后，单击 " **保存**"。 然后在左侧导航菜单中单击 " **身份验证/授权** "，并将其更新为所需的身份验证级别。 建议将 Azure Active Director (Azure AD) express。 

### <a name="test-the-app"></a>测试应用

请在 Azure Web 应用中进行操作并验证部署是否成功，以及 Web 应用是否正在运行。 导航到配置的 url： `<yourapp>.azurewebsites.net` 查看正在运行的应用程序。

![测试部署](./media/spatial-analysis/solution-app-output.png)

## <a name="next-steps"></a>后续步骤

* [配置空间分析操作](./spatial-analysis-operations.md)
* [日志记录和故障排除](spatial-analysis-logging.md)
* [照相机位置指南](spatial-analysis-camera-placement.md)
* [区域和线条位置指南](spatial-analysis-zone-line-placement.md)
