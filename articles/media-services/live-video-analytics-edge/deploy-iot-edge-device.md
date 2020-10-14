---
title: 在 IoT Edge 设备上部署实时视频分析 - Azure
description: 本文列出了将帮助你在 IoT Edge 设备上部署实时视频分析的步骤。 你可能会在以下情况下执行此操作，例如：如果你有权访问本地 Linux 计算机并且/或之前创建了 Azure 媒体服务帐户。
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: a64fc11d7afa70d5200fdbd24bd3facdb8a95a7e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019573"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>在 IoT Edge 设备上部署实时视频分析

本文列出了将帮助你在 IoT Edge 设备上部署实时视频分析的步骤。 你可能会在以下情况下执行此操作，例如：如果你有权访问本地 Linux 计算机并且/或之前创建了 Azure 媒体服务帐户。

> [!NOTE]
> IoT Edge 版本和更新版本的实时视频分析中提供了对 ARM64 设备的支持 `1.0.4` 。
> 支持在 ARM64 设备上运行 Azure IoT Edge 运行时，该支持为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

* 运行[受支持的 Linux 操作系统](../../iot-edge/support.md#operating-systems)之一的 x86 64 或 ARM64 设备
* 你拥有[所有者权限](../../role-based-access-control/built-in-roles.md#owner)的 Azure 订阅
* [创建和设置 IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)
* [注册 IoT Edge 设备](../../iot-edge/how-to-register-device.md)
* [在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge-linux.md)
* [创建 Azure 媒体服务帐户](../latest/create-account-howto.md)

    * 使用以下区域之一：美国东部2、美国东部、美国中部、美国中北部、日本东部、美国西部、美国西部2、美国中部、加拿大东部、英国南部、法国中部、法国南部、瑞士北部、瑞士西部和日本西部。
    * 建议使用常规用途 v2 (GPv2) 存储帐户

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>配置 Azure 资源以使用实时视频分析

### <a name="create-custom-azure-resource-manager-role"></a>创建自定义 Azure 资源管理器角色

请参阅[创建自定义 Azure 资源管理器角色](create-custom-azure-resource-manager-role-how-to.md)，并将其分配到用于实时视频分析的服务主体。

### <a name="set-up-a-premium-streaming-endpoint"></a>设置高级流式处理终结点

如果你打算使用实时视频分析将视频录制到云中，然后在重播之前按顺序使用[查询 API](playback-recordings-how-to.md#query-api)，则建议更新媒体服务以使用[高级流式处理终结点](../latest/streaming-endpoint-concept.md#types)。  

这是可选步骤。 可以使用此 Azure CLI 命令执行此操作：

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

可以使用此命令启动流式处理终结点 

> [!IMPORTANT]
> 此时，你的订阅将开始计费。

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

按照本文中的步骤获取访问媒体服务 API 的凭据：[访问媒体服务 API](../latest/access-api-howto.md?tabs=portal)，并选择“门户”选项卡。

## <a name="create-and-use-local-user-account-for-deployment"></a>创建并使用本地用户帐户进行部署
若要运行 IoT Edge 上的实时视频分析模块，请创建权限尽可能少的本地用户帐户。 例如，在 Linux 计算机上运行以下命令：

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>授予设备存储权限

现在，你创建了一个本地用户帐户， 

* 需要一个本地文件夹来存储应用程序配置数据。 使用以下命令创建文件夹并向本地用户帐户授予写入该文件夹的权限：

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* 还需要一个文件夹来[将视频录制到本地文件](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)。 同样，使用以下命令创建一个本地文件夹：

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>部署实时视频分析 Edge 模块

IoT Edge 上的实时视频分析公开记录在[模块孪生配置架构](module-twin-configuration-schema.md)中的模块孪生属性。 

### <a name="deploy-using-the-azure-portal"></a>使用 Azure 门户进行部署

Azure 门户引导你创建部署清单并将部署推送到 IoT Edge 设备。  
#### <a name="select-your-device-and-set-modules"></a>选择设备并设置模块

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
    
    * **IoT Edge 模块名称**：lvaEdge
    * **映像 URI**：mcr.microsoft.com/media/live-video-analytics:1.0    
    
    ![屏幕截图显示 "模块设置" 选项卡。](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > 如本过程所述，在“模块设置”、“容器创建选项”和“模块孪生设置”选项卡上指定值之前，请勿选择“添加”   。
    
    > [!WARNING]
    > 当你调用模块时，Azure IoT Edge 会区分大小写。 请记下用作模块名称的确切字符串。`

1. 打开“环境变量”选项卡。
   
   在显示![环境变量](./media/deploy-iot-edge-device/environment-variables.png)的输入框中添加以下值 

1. 打开“容器创建选项”选项卡。

    ![容器创建选项](./media/deploy-iot-edge-device/container-create-options.png)
 
    将以下 JSON 复制并粘贴到框中，从而限制模块生成的日志文件的大小。
    
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
   
   JSON 中的 "Binds" 部分有 2 个条目：
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices"：这用于从容器绑定永久性应用程序配置数据并将其存储在边缘设备上。
   1. "/var/media:/var/media"：这将绑定边缘设备和容器之间的媒体文件夹。 这用于在运行支持在边缘设备上存储视频剪辑的媒体图拓扑时存储视频录制。
   
1. 在“模块孪生设置”选项卡上，复制以下 JSON 并将其粘贴到框中。
 
    ![孪生设置](./media/deploy-iot-edge-device/twin-settings.png)

    IoT Edge 上的实时视频分析需要一组必需的孪生属性才能运行，如[模块孪生配置架构](module-twin-configuration-schema.md)中所列。  

    需要输入到模块孪生设置编辑框中的 JSON 将如下所示：    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    以下是上述 JSON 所需的属性，  
    * {subscriptionID} - 这是你的 Azure 订阅 ID
    * {resourceGroupName} - 这是你的媒体服务帐户所属的资源组
    * {AMS-account-name} - 这是你的媒体服务帐户名称
    
    若要获取其他值，请查看[访问 Azure 媒体服务 API](../latest/access-api-howto.md?tabs=portal)，并选择“门户”选项卡。  
    * aadTenantId - 这是你的租户 ID，与上述链接中的 "AadTenantId" 相同。
    * aadServicePrincipalAppId - 这是你的媒体服务帐户的服务主体的应用 ID，与上述链接中的 "AadClientId" 相同。
    * aadServicePrincipalSecret - 这是服务主体的密码，与上述链接中的 "AadSecret" 相同。

    下面是一些可添加到 JSON 的其他建议属性，将有助于监视模块。 有关详细信息，请参阅[监视和日志记录](monitoring-logging.md)：
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    以下是可以在 JSON 中添加的一些可选属性：
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > 出于教程和快速入门的目的，孪生属性 allowUnsecuredEndpoints 被设置为了 true。   
   在生产环境中运行时，应将此属性设置为 false。 这将确保应用程序将阻止所有不安全的终结点，并且需要有效的连接凭据才能运行图形拓扑。  
   
    选择“添加”以添加模块孪生属性。
1. 在完成时选择“下一步:路由”继续转到路由部分。
    指定路由。

保留默认路由并选择“下一步:  查看 + 创建”继续转到查看部分。

#### <a name="review-deployment"></a>评审部署

评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 另外还有两个模块已声明但却未添加，它们是：$edgeAgent 和 $edgeHub。 这两个模块构成 IoT Edge 运行时，并且是每个部署中所需的默认模块。

查看部署信息，然后选择“创建”。

### <a name="verify-your-deployment"></a>验证部署

创建部署后，返回到 IoT 中心的“IoT Edge”页。

1. 选择用作部署目标的 IoT Edge 设备，以打开其详细信息。
2. 在设备详细信息中，验证 Blob 存储模块是否已列为“在部署中指定”和“由设备报告”。

可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。
状态代码：200 -OK 表示 [IoT Edge 运行时](../../iot-edge/iot-edge-runtime.md)状况良好并且运行正常。

![屏幕截图显示 IoT Edge 运行时的状态值。](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>调用直接方法

接下来，让我们通过调用直接方法来测试示例。 阅读 [IoT Edge 上的实时视频分析的直接方法](direct-methods.md)，了解 lvaEdge 模块提供的所有直接方法。

1. 单击创建的边缘模块，此操作会将你转到其配置页面。  

    ![屏幕截图显示 edge 模块的配置页。](./media/deploy-iot-edge-device/modules.png)
1. 单击“直接方法”菜单选项。

    > [!NOTE] 
    > 正如你在当前页面上看到的那样，你需要在连接字符串部分中添加一个值。 无需隐藏或更改“设置名称”部分中的任何内容。 可以让它处于公开状态。

    ![直接方法](./media/deploy-iot-edge-device/module-details.png)
1. 在“方法名称”框中输入“GraphTopologyList”。
1. 接下来，复制以下 JSON 有效负载并将其粘贴到有效负载框中。
    
    ```
    {
        "@apiVersion" : "1.0"
    }
    ```
1. 单击页面顶部的“调用方法”选项

    ![直接方法](./media/deploy-iot-edge-device/direct-method.png)
1. 你应该会在“结果”框中看到状态 200 消息

    ![状态 200 消息](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>后续步骤

试用[快速入门：入门 - IoT Edge 上的实时视频分析](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> 如果继续执行上述快速入门，请在使用 Visual Studio Code 调用直接方法时，将使用通过本文添加到 IoT 中心的设备，而不是使用默认值 `lva-sample-device` 。