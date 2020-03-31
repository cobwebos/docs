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
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471279"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>如何在 Kubernetes 上安装 IoT Edge（预览）

IoT Edge 可与 Kubernetes 集成，将 Kubernetes 用作可复原的且高度可用的基础结构层。 以下是此支持适用于高级 IoT Edge 解决方案的位置：

![k8s 简介](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>此集成的一个很好的心理模型是认为 Kubernetes 是 IoT Edge 应用程序除了 Linux 和 Windows 之外可以运行的另一个操作环境。

## <a name="architecture"></a>体系结构 
在库伯奈斯，IoT 边缘为边缘工作负载部署提供*自定义资源定义*（CRD）。 IoT 边缘代理承担*CRD 控制器*的角色，该控制器将云托管的所需状态与本地群集状态协调。

模块生存期由 Kubernetes 计划程序管理，该计划程序将维护模块的可用性及选择其位置。 IoT Edge 管理顶层运行的边缘应用程序平台，会持续将 IoT 中心内指定的所需状态与边缘群集上的状态相协调。 应用模型仍然是基于 IoT Edge 模块和路由的熟悉模型。 IoT 边缘代理控制器将 IoT Edge 的应用程序模型*自动*翻译到 Kubernetes 本机构造，如 pod、部署、服务等。

下面是概要性的体系结构示意图：

![kubernetes 体系结构](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

边缘部署的每个组件划归到特定于设备的 Kubernetes 命名空间，因而可在多个边缘设备及其部署之间共享相同的群集资源。

>[!NOTE]
>Kubernetes 上的 IoT Edge 目前以[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)提供。

## <a name="tutorials-and-references"></a>教程和参考资料 

有关详细信息，请参阅[Kubernetes 上的 IoT 边缘预览文档迷你网站](https://aka.ms/edgek8sdoc)，包括深入教程和参考。
