---
title: 在 IoT Edge 设备上部署实时视频分析-Azure
description: 本文列出了可帮助你在 IoT Edge 设备上部署实时视频分析的步骤。 例如，如果你有权访问本地 Linux 计算机，并且/或者以前创建了 Azure 媒体服务帐户，则可以执行此操作。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: eaaa793bb5b84ac4ae352f242215b8d3e7d56cf1
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026961"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>在 IoT Edge 设备上部署实时视频分析

本文列出了可帮助你在 IoT Edge 设备上部署实时视频分析的步骤。 例如，如果你有权访问本地 Linux 计算机，并且/或者以前创建了 Azure 媒体服务帐户，则可以执行此操作。


## <a name="prerequisites"></a>先决条件

* 符合实时视频分析的硬件/软件约束的 Linux 计算机
* 拥有[所有者权限](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)的 Azure 订阅
* [创建和设置 IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)
* [注册 IoT Edge 设备](https://docs.microsoft.com/azure/iot-edge/how-to-register-device)
* [在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)
* [创建 Azure 媒体服务帐户](../latest/create-account-howto.md)
    * 使用以下区域之一：美国东部2、美国中部、美国中北部、日本东部、美国西部2、美国中部、加拿大东部、英国南部、法国中部、法国南部、瑞士北部、瑞士西部和日本西部。
    * 建议使用常规用途 v2 （GPv2）存储帐户。

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>使用实时视频分析配置 Azure 资源

### <a name="create-custom-azure-resource-manager-role"></a>创建自定义 Azure 资源管理器角色

请参阅[创建自定义 Azure 资源管理器角色](create-custom-azure-resource-manager-role-how-to.md)并将其分配到用于实时视频分析的服务主体。

### <a name="set-up-a-premium-streaming-endpoint"></a>设置高级流式处理终结点

如果你想要使用实时视频分析将视频录制到云，然后再播放视频，则应该将媒体服务更新为使用[高级流式处理终结点](../latest/streaming-endpoint-concept.md#types)。  

这是可选步骤。 您可以使用此 Azure CLI 命令执行此操作：

```azure-cli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

可以使用此命令启动流式处理终结点 

> [!IMPORTANT]
> 此时，你的订阅将开始计费。

```azure-cli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

按照本文中的步骤操作，获取访问媒体服务 Api 的凭据：[访问媒体服务 api](../latest/access-api-howto.md#use-the-azure-portal)。

## <a name="create-and-use-local-user-account-for-deployment"></a>创建和使用本地用户帐户进行部署
若要在 IoT Edge 模块上运行实时视频分析，请创建一个具有尽可能少权限的本地用户帐户。 例如，在 Linux 计算机上运行以下命令：

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>授予设备存储的权限

现在，你已创建了一个本地用户帐户， 

* 你将需要一个用于存储应用程序配置数据的本地文件夹。 使用以下命令创建文件夹并向 localuser 帐户授予对该文件夹的写入权限：

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* 还需要一个文件夹以将[视频录制到本地文件](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)。 使用以下命令创建相同的本地文件夹：

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>部署实时视频分析边缘模块

<!-- (To JuliaKo: this is similar to https://docs.microsoft.com/azure/iot-edge/how-to-deploy-blob)-->
IoT Edge 上的实时视频分析公开模块克隆[配置架构](module-twin-configuration-schema.md)中记录的模块克隆属性。 

### <a name="deploy-using-the-azure-portal"></a>使用 Azure 门户部署

Azure 门户引导你创建部署清单并将部署推送到 IoT Edge 设备。
选择设备

1. 登录 [Azure 门户](https://ms.portal.azure.com/)，导航到 IoT 中心。
1. 从菜单中选择“IoT Edge”  。
1. 在设备列表中单击目标设备的 ID。
1. 选择“设置模块”  。

#### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供了一个引导你完成创建部署清单的向导。 该向导将三个步骤整理成多个选项卡：“模块”、“路由”和“查看 + 创建”    。

#### <a name="add-modules"></a>添加模块

1. 在页面的“IoT Edge 模块”部分，单击“添加”下拉列表并选择“IoT Edge 模块”，显示“添加 IoT Edge 模块”页面     。
1. 在“模块设置”选项卡上，提供模块的名称，然后指定容器映像 URI  ：   
    示例:
    
    * **IoT Edge 模块名称**： lvaEdge
    * **映像 URI**： mcr.microsoft.com/media/live-video-analytics:1。0    
    
    ![添加](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > 请不要选择 "**添加**"，直到在 "**模块设置**"、"**容器创建选项**" 和 "**模块克隆设置**" 选项卡上指定值，如本过程中所述。
    
    > [!IMPORTANT]
    > 调用模块时，Azure IoT Edge 区分大小写。 记下用作模块名称的字符串。

1. 打开 "**环境变量**" 选项卡。
   
   将以下 JSON 复制并粘贴到框中，以提供用于保存应用程序数据和视频输出的用户 ID 和组 ID。
    ```   
   {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
     ``` 

1. 打开“容器创建选项”  选项卡。

    ![容器创建选项](./media/deploy-iot-edge-device/container-create-options.png)
 
    将以下 JSON 复制并粘贴到框中，以限制模块生成的日志文件的大小。
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   JSON 中的 "绑定" 部分有2个条目：
   1. "/var/lib/azuremediaservices：/var/lib/azuremediaservices"：用于绑定容器中的持久性应用程序配置数据，并将其存储在边缘设备上。
   1. "/var/media：/var/media"：这会将边缘设备和容器之间的媒体文件夹绑定在一起。 当您运行支持在边缘设备上存储视频剪辑的 media graph 拓扑时，此用于存储视频录制。
   
1. 在“模块孪生设置”选项卡上，复制以下 JSON 并将其粘贴到框中  。
 
    ![克隆设置](./media/deploy-iot-edge-device/twin-settings.png)

    IoT Edge 上的实时视频分析需要一组必需的克隆属性才能运行，如[模块克隆配置架构](module-twin-configuration-schema.md)中所列。  

    需要输入到模块中的设置编辑框的 JSON 如下所示：    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    下面是**所需**的属性和上述 JSON 的属性，  
    * {subscriptionID}-这是你的 Azure 订阅 ID
    * {resourceGroupName}-你的媒体服务帐户所属的资源组
    * {AMS-account-name}-这是你的媒体服务帐户的名称
    
    若要获取其他值，请参阅[访问 Azure 媒体服务 API](../latest/access-api-howto.md#use-the-azure-portal)。  
    * aadTenantId-这是租户的 ID，与上述链接中的 "AadTenantId" 相同。
    * aadServicePrincipalAppId-这是媒体服务帐户的服务主体的应用 ID，与上述链接中的 "AadClientId" 相同。
    * aadServicePrincipalSecret-这是服务主体的密码，与上述链接中的 "AadSecret" 相同。

    下面是一些可添加到 JSON 的附加**建议**属性，这些属性将有助于监视模块。 有关详细信息，请参阅[监视和日志记录](monitoring-logging.md)：
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    下面是可以在 JSON 中添加的一些**可选**属性：
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```
   [!Note]
   对于教程和快速入门，将**allowUnsecuredEndpoints**属性设置为 true。   
   在生产环境中运行时，应将此属性设置为**false** 。 这将确保应用程序将阻止所有不安全的终结点，若要运行图形拓扑，将需要有效的连接凭据。  
   
    选择 "添加" 以添加模块克隆属性。
1. 在完成时选择“下一步:路由”继续转到路由部分。
    指定路由。

保留默认路由并选择“下一步:  查看 + 创建”继续转到查看部分。

#### <a name="review-deployment"></a>评审部署

评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 另外还有两个模块已声明但却未添加，它们是： $edgeAgent 和 $edgeHub。 这两个模块构成 IoT Edge 运行时，且是每个部署中所需的默认设置。

查看部署信息，然后选择“创建”  。

### <a name="verify-your-deployment"></a>验证部署

创建部署后，返回到 IoT 中心的“IoT Edge”页。 

1.  选择用作部署目标的 IoT Edge 设备，以打开其详细信息。
2.  在 "设备详细信息" 中，验证 blob 存储模块是否被列为 "**部署" 中指定的和 "设备报告的**"。

可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。
状态代码：200–正常表示[IoT Edge 运行时运行](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime)正常，且操作良好。

![状态](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>调用直接方法

接下来，通过调用直接方法来测试示例。 阅读[IoT Edge 上实时视频分析的直接方法](direct-methods.md)，以了解我们的 lvaEdge 模块提供的所有直接方法。

1. 单击创建的边缘模块会将你转到其配置页面。  

    ![模块](./media/deploy-iot-edge-device/modules.png)
1. 单击 "直接方法" 菜单选项。

    > [!NOTE] 
    > 您将需要在 "连接字符串" 部分中添加一个值，就像在当前页上看到的一样。 不需要在 "**设置名称**" 部分中隐藏或更改任何内容。 可以让它成为公共的。

    ![直接方法](./media/deploy-iot-edge-device/module-details.png)
1. 接下来，在 "方法名称" 框中输入 "GraphTopologyList"。
1. 接下来，在 "负载" 框中复制并粘贴以下 JSON 有效负载。
    
    ```
    {
        "@apiVersion" : "1.0"
    }
    ```
1. 在页面顶部单击 "调用方法" 选项

    ![直接方法](./media/deploy-iot-edge-device/direct-method.png)
1. 你应会在 "结果" 框中看到状态200消息

    ![状态200消息](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>后续步骤

[快速入门：入门 - IoT Edge 上的实时视频分析](get-started-detect-motion-emit-events-quickstart.md)
