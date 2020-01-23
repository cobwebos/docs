---
title: 如何在 Kubernetes 上安装 IoT Edge |Microsoft Docs
description: 了解如何使用本地开发群集环境在 Kubernetes 上安装 IoT Edge
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d11d23cf7d96482028a9d3738196fc5a787fec91
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510203"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>如何在 Kubernetes 上安装 IoT Edge （预览版）

IoT Edge 可以将其作为可复原且高度可用的基础结构层与 Kubernetes 集成。 它向 Kubernetes API 服务器注册 IoT Edge*自定义资源定义*（.crd）。 此外，它还提供了一个运算符（IoT Edge 代理），该*操作员*可协调云托管的所需状态和本地群集状态。

模块生存期由 Kubernetes 计划程序管理，后者维护模块可用性并选择其位置。 IoT Edge 管理在顶部运行的边缘应用程序平台，并通过边缘群集上的状态持续协调 IoT 中心中指定的所需状态。 边缘应用程序模型仍是基于 IoT Edge 模块和路由的熟悉的模型。 IoT Edge 代理运算符*自动*转换为 Kubernetes natives 构造，如 pod、部署、服务等。

下面是一个高级体系结构关系图：

![kubernetes](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

边缘部署的每个组件的作用域都是特定于设备的 Kubernetes 命名空间，因此可以在多个边缘设备及其部署之间共享相同的群集资源。

>[!NOTE]
>Kubernetes 上的 IoT Edge 为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="install-locally-for-a-quick-test-environment"></a>在本地安装，用于快速测试环境

### <a name="prerequisites"></a>必备组件

* Kubernetes 1.10 或更高版本。 如果没有现有群集设置，可以对本地群集环境使用[Minikube](https://kubernetes.io/docs/setup/minikube/) 。

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide)，Kubernetes 包管理器。

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)用于查看群集并与之进行交互。

### <a name="setup-steps"></a>设置步骤

1. 启动**Minikube**

    ``` shell
    minikube start
    ```

1. 初始化群集中的**Helm**服务器组件（*tiller*）

    ``` shell
    helm init
    ```

1. 添加 IoT Edge 存储库并更新 helm 安装

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)，[注册 IoT Edge 设备](how-to-register-device.md)，并记下其连接字符串。

1. 将 iotedged 和 IoT Edge 代理安装到群集中

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

    在群集命名空间下，将按照约定*msiot-\<iothub >-\<edgedevice 名称 >* 来查看 IoT Edge 设备的名称。 IoT Edge 代理和 iotedged pod 应在此命名空间中启动并运行。

1. 使用快速入门的 "[部署模块](quickstart-linux.md#deploy-a-module)" 一节中的步骤添加模拟温度传感器模块。 IoT Edge 模块管理从 IoT 中心门户完成，就像任何其他 IoT Edge 设备一样。 不建议通过 Kubernetes 工具对模块配置进行本地更改，因为它们可能会被覆盖。

1. 几秒钟后，在仪表板中的 "边缘设备命名空间" 下刷新 "pod **" 页将**列出 IoT Edge 集线器和模拟传感器盒，它们在运行时将 IoT Edge 集线器 pod 引入数据插入到 IoT 中心。

## <a name="clean-up-resources"></a>清理资源

若要删除边缘部署创建的所有资源，请将以下命令与上一部分的步骤5中使用的名称一起使用。

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>后续步骤

### <a name="deploy-as-a-highly-available-edge-gateway"></a>部署为高可用边缘网关

Kubernetes 群集中的边缘设备可用作下游设备的 IoT 网关。 可以将其配置为可灵活地应对节点故障，从而向边缘部署提供高可用性。 若要在此方案中使用 IoT Edge，请参阅此[详细演练](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes)。
