---
title: 通过用于空间分析的计算机视觉分析实时视频 - Azure
description: 本教程展示了如何结合使用实时视频分析和 Azure 认知服务中的计算机视觉空间分析 AI 功能，分析来自（模拟）IP 相机的实时视频源。
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: cad96847d6fbf682f1d694b0c8c255b3725e96d1
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824126"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>通过用于空间分析的计算机视觉（预览版）分析实时视频

本教程展示了如何将实时视频分析与 [Azure 认知服务中的空间分析 AI 服务的计算机视觉](https://azure.microsoft.com/services/cognitive-services/computer-vision/)结合使用，以分析来自（模拟）IP 相机的实时视频源。 将介绍如何使用此推理服务器来分析流式处理视频，以便了解人员之间的空间关系和物理空间中的移动。  视频源中的一部分帧将发送到此推理服务器，结果将发送到 IoT Edge 中心。在满足某些条件时，将录制视频剪辑并将其存储为 Azure 媒体服务资产。

在本教程中，将：

> [!div class="checklist"]
> * 设置资源。
> * 检查代码。
> * 运行示例代码。
> * 监视事件。
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建议的读前准备

在开始之前，请阅读以下文章：

* [IoT Edge 上的实时视频分析概述](overview.md)
* [IoT Edge 上的实时视频分析术语](terminology.md)
* [媒体图概念](media-graph-concept.md)
* [基于事件的视频录制](event-based-video-recording-concept.md)
* [教程：开发 IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [在 Azure Stack Edge 上部署实时视频分析](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>先决条件

下面是将空间分析模块连接到实时视频分析模块的先决条件。

* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)。 请确保具有 [Azure IoT Tools 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
    * 确保开发计算机连接到的网络允许基于端口 5671 的高级消息队列协议。 此设置使 Azure IoT Tools 可以与 Azure IoT 中心通信。
* 带 GPU 加速的 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)。  
    虽然建议你使用带 GPU 加速的 Azure Stack Edge，但该容器可以在带 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/) 的任何其他设备上运行。 
* 用于空间分析的 [Azure 认知服务计算机视觉容器](https://azure.microsoft.com/services/cognitive-services/computer-vision/)。  
    若要使用此容器，你必须有一个计算机视觉资源，以获取关联的 **API 密钥**和**终结点 URI**。 可以从 Azure 门户的计算机视觉“概览”页和“密钥”页获取 API 密钥。 密钥和终结点是启动容器所必需的。

## <a name="overview"></a>概述

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="空间分析概述":::
 
此图显示了本教程中的信号如何流动。 [Edge 模块](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](media-graph-concept.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到[帧速率筛选器处理器](media-graph-concept.md#frame-rate-filter-processor)节点。 该处理器会限制到达 MediaGraphCognitiveServicesVisionExtension 处理器节点的视频流的帧速率。

MediaGraphCognitiveServicesVisionExtension 节点充当代理角色。 它将视频帧转换为指定的图像类型。 然后，它将图像通过**共享内存**中继到另一个 Edge 模块，该模块在 gRPC 终结点后运行 AI 操作。 在此示例中，该 Edge 模块是空间分析模块。 MediaGraphCognitiveServicesVisionExtension 处理器节点执行两项操作：

* 它收集结果，并将事件发布到 [IoT 中心接收器](media-graph-concept.md#iot-hub-message-sink)节点。 然后该节点将这些事件发送到 [IoT Edge 中心](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub)。 
* 它还通过使用[信号入口处理器](media-graph-concept.md#signal-gate-processor)从 RTSP 源捕获 30 秒视频剪辑，并将其存储为媒体服务资产。

## <a name="create-the-computer-vision-resource"></a>创建计算机视觉资源

你需要通过 [Azure 门户](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)或 Azure CLI 创建计算机视觉类型的 Azure 资源。 如果对容器的访问请求获得批准并且你已注册 Azure 订阅 ID，你将能够创建资源。 转到 https://aka.ms/csgate 提交你的用例和你的 Azure 订阅 ID。  你需要使用在“请求访问”窗体上提供的同一 Azure 订阅来创建 Azure 资源。

### <a name="gathering-required-parameters"></a>收集必需的参数

所有认知服务容器（包括空间分析容器）都需要三个主要参数。 最终用户许可协议 (EULA) 的值必须为 accept。 此外，终结点 URI 和 API 密钥都是必需的。

### <a name="keys-and-endpoint-uri"></a>密钥和终结点 URI

此密钥用于启动空间分析容器，在相应认知服务资源的 Azure 门户的 `Keys and Endpoint` 页上提供。 导航到该页并找到密钥和终结点 URI。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="空间分析概述":::

## <a name="set-up-azure-stack-edge"></a>设置 Azure Stack Edge

按照[这些步骤](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep)设置 Azure Stack Edge，并继续执行以下步骤，以便部署实时视频分析和空间分析模块。

## <a name="set-up-your-development-environment"></a>设置开发环境

1. 从此位置克隆存储库： https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp 。
1. 在 Visual Studio Code 中，打开下载的存储库所在的文件夹。
1. 在 Visual Studio Code 中，转到 src/cloud-to-device-console-app 文件夹。 在该文件中，创建一个文件并将其命名为 appsettings.json。 该文件将包含运行程序所需的设置。
1. 通过执行以下步骤，从 Azure Stack Edge 获取 IotHubConnectionString：

    * 在 Azure 门户中转到你的 IoT 中心，然后在左侧导航窗格中单击“`Shared access policies`”。
    * 单击 `iothubowner` 以获取共享访问密钥。
    * 复制 `Connection String – primary key`，并将其粘贴到 VSCode 上的输入框中。
    
        连接字符串将如下所示： <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. 将以下内容复制到文件中。 确保对变量进行替换。
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. 转到 src/edge 文件夹并创建一个名为 .env 的文件 。
1. 复制 /clouddrive/lva-sample/edge-deployment/.env 文件的内容。 文本应类似于以下代码。

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>设置部署模板  

在 /src/edge/deployment.spatialAnalysis.template.json 中查找部署文件。 模板中有 lvaEdge 模块、rtspsim 模块和空间分析模块。

在部署模板文件中，需要注意以下事项：

1. 设置端口绑定。
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. lvaEdge 模块和空间分析模块 createOptions 中的 `IpcMode` 应当相同并设置为 host。
1. 若要使 RTSP 模拟器正常工作，请确保已设置“卷边界”。 有关详细信息，请参阅[设置 Docker 卷装载](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts)。

    1. [连接到 SMB 共享](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share)并将[示例推土机视频文件](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv)复制到本地共享。
    1. 可以看到 rtspsim 模块包含以下配置：
        
        ```json
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
## <a name="generate-and-deploy-the-deployment-manifest"></a>生成并部署部署清单

部署清单定义要部署到边缘设备的模块。 它还定义了这些模块的配置设置。

请按照以下步骤从模板文件生成清单，然后将其部署到边缘设备。

1. 打开 Visual Studio Code。
1. 在“AZURE IOT 中心”窗格旁，选择“更多操作”图标以设置 IoT 中心连接字符串 。 可以复制 src/cloud-to-device-console-app/appsettings.json 文件中的字符串。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="空间分析概述":::
1. 右键单击 `src/edge/deployment.spatialAnalysis.template.json` 并选择“生成 IoT Edge 部署清单”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="空间分析概述":::
    
    此操作应在 src/edge/config 文件夹中创建一个名为“deployment.amd64.json”的清单文件 。
1. 右键单击 `src/edge/config/deployment.spatialAnalysis.amd64.json`，选择“为单个设备创建部署”，然后选择边缘设备的名称。
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="空间分析概述":::   
1. 如果系统提示你选择 IoT 中心设备，请从下拉菜单中选择你的 Azure Stack Edge 名称。
1. 大约 30 秒后，在该窗口的左下角刷新 Azure IoT 中心。 边缘设备现在显示以下已部署的模块：
    
    * IoT Edge 上的实时视频分析（模块名称为 lvaEdge）
    * 实时流式处理协议 (RTSP) 模拟器（模块名称为 rtspsim）。
    * 空间分析（模块名称为 spatialAnalysis）。
    
如果你成功部署，则“输出”中将显示类似于以下内容的消息：

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

然后，你可以在 Devices/Modules 下查找 `lvaEdge`、`rtspsim`、`spatialAnalysis` 和 `rtspsim` 模块，其状态应当为“running”。

## <a name="prepare-to-monitor-events"></a>准备监视事件

若要查看这些事件，请执行以下步骤：

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="空间分析概述":::
1. 搜索并启用“显示详细消息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="空间分析概述":::
1. 打开“资源管理器”窗格，然后在左下角查找“Azure IoT 中心”。
1. 展开“设备”节点。
1. 右键单击你的 Azure Stack Edge，然后选择“开始监视内置事件终结点”。
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="空间分析概述":::
     
## <a name="run-the-program"></a>运行程序

有一个 program.cs，它将调用 src/cloud-to-device-console-app/operations.json 中的直接方法。 我们需要设置 operations.json 并提供一个拓扑以使用媒体图。  

在 operations.json 中：

* 设置如下拓扑（topologyFile 用于本地拓扑，topologyUrl 用于联机拓扑）：

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysisTopology.json"
    }
},
```

* 创建图形实例并在拓扑中设置参数，如下所示：

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
                },
                {
                    "name": "rtspUserName",
                    "value": "testuser"
                },
                {
                    "name": "rtspPassword",
                    "value": "testpassword"
                }
            ]
        }
    }
},
```
* 将链接更改为图拓扑：

`topologyUrl` : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/topology.json"

在 GraphInstanceSet 下，编辑图形拓扑的名称以匹配前面链接中的值：

`topologyName`：InferencingWithCVExtension

在 GraphTopologyDelete 下，编辑名称：

`name`：InferencingWithCVExtension

>[!Note]
查看是否使用 MediaGraphRealTimeComputerVisionExtension 连接到空间分析模块。 将 ${grpcUrl} 设置为 tcp://spatialAnalysis:<PORT_NUMBER>，如 tcp://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcUrl}",
    "credentials": {
        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
        "username": "${spatialanalysisusername}",
        "password": "${spatialanalysispassword}"
    }
    },
    "image": {
    "scale": {
        "mode": "pad",
        "width": "1408",
        "height": "786"
    },
    "format": {
        "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
        "pixelFormat": "bgr24"
    }
    },
    "inputs": [
    {
        "nodeName": "frameRateFilter"
    }
    ]
}
```

运行调试会话并按照终端说明进行操作。它将设置拓扑、设置图形实例、激活图形实例，并最终删除资源。

## <a name="interpret-results"></a>解释结果

实例化媒体图时，应会看到“MediaSessionEstablished”事件。此处是一个[示例 MediaSessionEstablished 事件](detect-motion-emit-events-quickstart.md#mediasessionestablished-event)。

空间分析模块还会将 AI 见解事件发送到实时视频分析，然后再发送到 IoTHub。它还会显示在输出中。 ENTITY 是检测对象，EVENT 是空间分析事件。 此输出将被传递到实时视频分析。

personZoneEvent 的示例输出（来自 cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics 操作）：

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>后续步骤

通过在部署清单文件的图形节点中切换“enabled”标志，尝试 `spatialAnalysis` 模块提供的各种操作，例如 **personCount** 和 **personDistance**。
>[!Tip]
> 使用帧中有多个人的[示例视频文件](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv)。

> [!NOTE]
> 一次只能运行一个操作。 因此，请确保只将一个标志设置为 **true**，将其他标志设置为 **false**。
