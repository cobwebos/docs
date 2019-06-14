---
title: 如何在 Kubernetes 上安装 IoT Edge | Microsoft Docs
description: 了解如何使用本地开发群集环境在 Kubernetes 上安装 IoT Edge
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160690"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>如何在 Kubernetes 上安装 IoT Edge（预览）

IoT Edge 可与 Kubernetes 集成，将 Kubernetes 用作可复原的且高度可用的基础结构层。 它会将 IoT Edge 自定义资源定义 (CRD) 注册到 Kubernetes API 服务器。  此外，它提供一个操作程序（IoT Edge 代理）用于将云托管所需状态与本地群集状态相协调。  

模块生存期由 Kubernetes 计划程序管理，该计划程序将维护模块的可用性及选择其位置。 IoT Edge 管理顶层运行的边缘应用程序平台，会持续将 IoT 中心内指定的所需状态与边缘群集上的状态相协调。 边缘应用程序模型仍是用户熟悉的基于 IoT Edge 模块和路由的模型。 IoT Edge 代理操作程序执行到 Kubernetes 本机构造（例如 pod、部署、服务等）的自动转换。 

下面是概要性的体系结构示意图：

![kubernetes 体系结构](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

边缘部署的每个组件划归到特定于设备的 Kubernetes 命名空间，因而可在多个边缘设备及其部署之间共享相同的群集资源。

>[!NOTE]
>Kubernetes 上的 IoT Edge 目前以[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)提供。

## <a name="install-locally-for-a-quick-test-environment"></a>在本地为快速测试环境安装 IoT Edge

### <a name="prerequisites"></a>必备组件

* Kubernetes 1.10 或更高版本。 如果尚未设置现有的群集，可以使用 [Minikube](https://kubernetes.io/docs/setup/minikube/) 作为本地群集环境。 

* Kubernetes 包管理器 [Helm](https://helm.sh/docs/using_helm/#quickstart-guide)。

* 用于查看群集以及与之交互的 [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

### <a name="setup-steps"></a>设置步骤

1. 启动 **Minikube**

    ``` shell
    minikube start
    ```

1. 在群集中初始化 **Helm** 服务器组件 (*tiller*)

    ``` shell
    helm init
    ```

1. 添加 IoT Edge 存储库并更新 helm 安装

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)、[注册 IoT Edge 设备](how-to-register-device-portal.md)，并记下其连接字符串。

1. 将 Iotedged 和 IoT Edge 代理安装到群集中

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. 在浏览器中打开 Kubernetes 仪表板

    ```shell
    minikube dashboard
    ```

    在群集命名空间下，会看到 IoT Edge 设备有一个遵循 *msiot-\<iothub-name>-\<edgedevice-name>* 约定的命名空间。 IoT Edge 代理和 iotedged pod 应已在此命名空间中启动并运行。

1. 使用本快速入门的[部署模块](quickstart-linux.md#deploy-a-module)部分所述的步骤添加模拟的温度传感器模块。 与在任何其他 IoT Edge 设备中一样，IoT Edge 模块管理是通过 IoT 中心门户进行的。 不建议通过 Kubernetes 工具对模块配置进行本地更改，因为这可能会覆盖配置。

1. 几秒钟后，在仪表板中的边缘设备命名空间下刷新“Pod”页会将 IoT Edge 中心和模拟传感器 pod 列为正在运行，此时 IoT Edge 中心 pod 正在将数据引入 IoT 中心。 

## <a name="clean-up-resources"></a>清理资源

若要删除边缘部署创建的所有资源，请结合上一部分的步骤 5 中使用的名称运行以下命令。

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>后续步骤

### <a name="deploy-as-a-highly-available-edge-gateway"></a>部署高可用性边缘网关 

Kubernetes 群集中的边缘设备可用作下游设备的 IoT 网关。 可将其配置为弹性应对节点故障，从而为边缘部署提供高可用性。 请参阅此[详细演练](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes)，了解如何在此方案中使用 IoT Edge。