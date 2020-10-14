---
title: 在 Azure Stack Edge 上部署实时视频分析
description: 本文列出了可帮助你在 Azure Stack 边缘部署实时视频分析的步骤。
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: f33b6fb0f0dc5c5b733a0fcb021e2792ce9c6ec6
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019590"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>在 Azure Stack Edge 上部署实时视频分析

本文列出了可帮助你在 Azure Stack 边缘部署实时视频分析的步骤。 设置并激活设备后，该设备便可用于实时视频分析部署。 

对于实时视频分析，我们将通过 IoT 中心进行部署，但 Azure Stack Edge 资源公开 Kubernetes API，该 API 允许客户部署可通过实时视频分析实现交互的其他非 IoT 中心识别解决方案。 

> [!TIP]
> 对于自定义部署，使用 Kubernetes (K8s) API 是一种高级案例。 建议客户创建边缘模块，并通过 IoT 中心将其部署到每个 Azure Stack Edge 资源，而不是使用 Kubernetes API。 本文将介绍如何使用 IoT 中心部署实时视频分析模块。

## <a name="prerequisites"></a>先决条件

* 拥有 [所有者权限](../../role-based-access-control/built-in-roles.md#owner)的 Azure 订阅。
* [Azure Stack 边缘](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)资源
   
* [IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)
* 实时视频分析模块的 [服务主体](./create-custom-azure-resource-manager-role-how-to.md#create-service-principal) 。

   使用 IoT 中心可用的其中一个区域：美国东部2、美国中部、美国中北部、日本东部、美国西部2、美国中部、加拿大东部、英国南部、法国中部、法国南部、瑞士北部、瑞士西部和日本西部。
* 存储帐户

    建议使用常规用途 v2 (GPv2) 存储帐户。  
    详细了解 [常规用途 v2 存储帐户](../../storage/common/storage-account-upgrade.md?tabs=azure-portal)。
* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)。 请确保具有 [Azure IoT Tools 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 确保开发计算机连接到的网络允许基于端口 5671 的高级消息队列协议。 此设置使 Azure IoT Tools 可以与 Azure IoT 中心通信。

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>配置使用实时视频分析 Azure Stack 边缘

Azure Stack Edge 是一种硬件即服务解决方案，是一种支持 AI 的边缘计算设备，具有网络数据传输功能。 详细了解 [Azure Stack Edge 和详细的设置说明](../../databox-online/azure-stack-edge-deploy-prep.md)。 若要开始，请按照以下链接中的说明进行操作：

* [Azure Stack 边缘/Data Box Gateway 资源创建](../../databox-online/azure-stack-edge-deploy-prep.md)
* [安装和设置](../../databox-online/azure-stack-edge-deploy-install.md)
* [连接和激活](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md)

### <a name="attach-an-iot-hub-to-azure-stack-edge"></a>将 IoT 中心附加到 Azure Stack 边缘

1. 在 [Azure 门户](https://ms.portal.azure.com)中，请切换到 Azure Stack Edge 资源，然后单击 "概述"。 在右窗格中的“计算”磁贴上，选择“开始”。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge.png" alt-text="Azure Stack Edge":::
1. 在“配置 Edge 计算”磁贴上，选择“配置计算”。 
1. 在“配置 Edge 计算”边栏选项卡上输入以下内容：
    
    | 字段|值|
    |---|---|
    |IoT 中心|选择“新建”或“现有”。 <br/>默认会使用标准层 (S1) 来创建 IoT 资源。 若要使用免费层 IoT 资源，请创建一个资源，然后选择现有的资源。<br/>在每种情况下，IoT 中心资源使用与 Azure Stack Edge 资源相同的订阅和资源组。|
    |名称|输入 IoT 中心资源的名称。|

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-get-started.png" alt-text="Azure Stack Edge":::
1. 选择“创建”  。 创建 IoT 中心资源需要花费几分钟时间。 创建 IoT 中心资源后，“配置计算”磁贴会更新，以显示计算配置。  若要确认是否已配置 Edge 计算角色，请在“配置计算”磁贴上选择“查看计算”。  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/edge-compute-config.png" alt-text="Azure Stack Edge" 卡，如下所示：
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/configure-edge-compute.png" alt-text="Azure Stack Edge":::
 
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>在 Azure Stack Edge 本地 UI 上启用计算先决条件

继续之前，请确保：

* 已激活 Azure Stack Edge 资源。
* 你有权访问运行 PowerShell 5.0 或更高版本的 Windows 客户端系统来访问 Azure Stack Edge 资源。
* 若要部署 Kubernetes 群集，需要通过 [本地 WEB UI](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup)配置 Azure Stack Edge 资源。 
    
    * 若要启用计算，请在设备的本地 web UI 中，切换到 "计算" 页。
    
        * 选择要为计算启用的网络接口。 选择“启用”。 启用计算会导致在设备上的网络接口上创建虚拟交换机。
        * 将 Kubernetes 测试节点 Ip 和 Kubernetes 外部服务 Ip 留空。
        * 选择应用-此操作需要大约2分钟。
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text="Azure Stack Edge" 部分下 Azure Stack Edge 门户中找到此信息。 ) 
            * 保存并关闭

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>使用 Azure 门户部署实时视频分析边缘模块

为此，我们只需执行 [通过 IoT 中心部署实时视频分析](deploy-iot-edge-device.md)的特定步骤。

1. 跳过用户和组创建步骤，转到 [部署实时视频分析边缘](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) 模块。 遵循此处所述的步骤。
1. 在容器创建选项中，无需设置环境变量。 因此，请跳过此步骤。
1. 打开“容器创建选项”选项卡。

   * 将以下 JSON 复制并粘贴到框中，从而限制模块生成的日志文件的大小。
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > 如果将 gRPC 协议与共享内存传输一起使用，则在实时视频分析和推理解决方案之间使用主机 IPC 模式来访问共享内存。
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > JSON 中的 "绑定" 部分有2个条目。 可随意更新边缘设备绑定，但请确保这些目录存在。
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices"：这用于从容器绑定永久性应用程序配置数据并将其存储在边缘设备上。
    * "/var/media:/var/media"：这将绑定边缘设备和容器之间的媒体文件夹。 这用于在运行支持在边缘设备上存储视频剪辑的媒体图拓扑时存储视频录制。
        
1. 继续执行文档中的步骤，并填写模块克隆设置。
1. 选择 " **下一步**：继续路由" 部分。 指定路由。

    保留默认路由并选择“下一步:  查看 + 创建”继续转到查看部分。
1. [查看并验证你的部署](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a> (可选) 安装 Docker 卷装载

若要查看工作目录中的数据，请按照以下步骤在部署之前设置 Docker 卷装载。 

这些步骤介绍了如何创建网关用户并设置文件共享，以查看实时视频分析工作目录和实时视频分析媒体文件夹的内容。

> [!NOTE]
> 支持绑定装载，但卷装入允许数据查看，如果需要，还可以远程复制。 可以同时使用 Bind 和批量装载，但不能指向相同的容器路径。

1. 打开 Azure 门户并中转到 Azure Stack Edge 资源。
1. 创建可访问共享的 **网关用户** 。
    
    1. 在左侧导航窗格中，单击 " **网关->用户**"。
    1. 单击 " **+ 添加用户** " 设置 "用户名" 和 "密码"。 建议 (： `lvauser`) 。
    1. 单击 " **添加**"。
    
1. 为实时视频分析暂留创建 **本地共享** 。

    1. 单击 " **网关->共享**"。
    1. 单击 " **+ 添加共享**"。
    1. 设置共享名称。 建议 (： `lva`) 。
    1. 将共享类型保留为 SMB。
    1. 请确保选中 **"使用与边缘计算的共享"** 。
    1. 确保选中 " **配置为边缘本地共享** "。
    1. 在 "用户详细信息" 中，向最近创建的用户授予对共享的访问权限。
    1. 单击 " **创建**"。
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Azure Stack Edge" **创建**"。    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Azure Stack Edge"
            }]
        }
    }
    ```

### <a name="verify-that-the-module-is-running"></a>验证模块是否正在运行

最后一步是确保模块按预期连接并运行。 对于 IoT 中心资源中的 IoT Edge 设备，模块的运行时状态应为正在运行。

若要验证模块是否正在运行，请执行以下操作：

1. 在 Azure 门户中，返回到 Azure Stack Edge 资源
1. 选择 "模块" 磁贴。 随后会转到“模块”边栏选项卡。 在模块列表中，找到已部署的模块。 所添加模块的运行时状态应为“正在运行”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Azure Stack Edge":::

### <a name="configure-the-azure-iot-tools-extension"></a>配置 Azure IoT Tools 扩展

按照以下说明使用 Azure IoT Tools 扩展连接到 IoT 中心。

1. 在 Visual Studio Code 中，选择 "查看 > 资源管理器"。 或是选择 Ctrl+Shift+E。
1. 在“资源管理器”选项卡的左下角，选择“Azure IoT 中心”。
1. 选择“更多选项”图标以查看上下文菜单。 然后选择“设置 IoT 中心连接字符串”。
1. 输入框出现时，在其中输入 IoT 中心连接字符串。 

   * 若要获取连接字符串，请转到 Azure 门户中的 IoT 中心，然后单击左侧导航窗格中的 "共享访问策略"。
   * 单击 "iothubowner 获取共享访问密钥"。
   * 复制 "连接字符串-主键" 并将其粘贴到 VSCode 上的输入框中。

   连接字符串将如下所示：<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   如果连接成功，边缘设备列表随即显示。 应会看到 Azure Stack 边缘。 现在你可以管理 IoT Edge 设备，并通过上下文菜单与 Azure IoT 中心进行交互。 若要查看边缘设备上部署的模块，请在 Azure Stack 设备下，展开 "模块" 节点。
    
## <a name="troubleshooting"></a>疑难解答

* Kubernetes API Access (kubectl) 。

    * 按照文档配置计算机以 [访问 Kubernetes 群集](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues)。
    * 所有已部署的 IoT Edge 模块都使用 `iotedge` 命名空间。 使用 kubectl 时，请确保包含该。
* 模块日志

    `iotedge`获取日志时无法访问此工具。 必须使用 [kubectl 日志](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  来查看文件的日志或管道。 示例： <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* Pod 和节点指标

    使用 [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  查看 pod 和 node 指标。  (此功能将在下一次 Azure Stack 边缘版本中可用。 >v2007) <br/>`kubectl top pods -n iotedge`
* 模块网络对于 Azure Stack Edge 上的模块发现，该模块必须在 createOptions 中具有主机端口绑定。 然后，就可以对该模块进行寻址 `moduleName:hostport` 。
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* 卷装入

    如果容器尝试将卷装载到现有的和非空目录，模块将无法启动。
* Shared Memory

    任何命名空间中通过使用主机 IPC 跨 pod 支持 Azure Stack 边缘资源上的共享内存。
    通过 IoT 中心配置边缘模块上的共享内存以进行部署。
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
*  (高级) Pod 共同位置

    使用 K8s 部署通过 gRPC 与实时视频分析通信的自定义推理解决方案时，需要确保将 pod 部署在与实时视频分析模块相同的节点上。

    * 选项 1-使用节点关联和内置节点标签作为共同位置。

    当前 NodeSelector 的自定义配置似乎不是用户无权在节点上设置标签的选项。 但根据客户的拓扑和命名约定，它们可能能够使用 [内置节点标签](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)。 通过实时视频分析来引用 Azure Stack 边缘资源的 nodeAffinity 部分可以添加到推理 pod 清单中，以实现归置。
    * 选项 2-将 Pod 相关性用于归置 (建议) 。
Kubernetes 支持 [Pod 关联](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  ，可以在同一节点上计划 Pod。 可以将引用实时视频分析模块的 podAffinity 部分添加到推理 pod 清单以实现归置。

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```

## <a name="next-steps"></a>后续步骤

可以通过调用直接方法来使用该模块分析实时视频流。 [调用模块上的直接方法](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) 。