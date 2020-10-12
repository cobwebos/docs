---
title: 通过 IoT Edge 模块在 Azure Stack Edge Pro GPU 上部署 Kubernetes 无状态应用 |Microsoft Docs
description: 介绍如何使用通过外部 IP 访问的 IoT Edge 模块在 Azure Stack Edge Pro GPU 设备上部署 Kubernetes 无状态应用程序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 4bc598080b96886e6734ac3709761465a1a28d49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899530"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>使用 IoT Edge 模块在 Azure Stack Edge Pro GPU 设备上运行 Kubernetes 无状态应用程序

本文介绍如何使用 IoT Edge 模块在 Azure Stack Edge Pro 设备上部署无状态应用程序。

若要部署无状态应用程序，你需要执行以下步骤：

- 在部署 IoT Edge 模块之前，请确保已完成先决条件。
- 添加一个 IoT Edge 模块来访问 Azure Stack Edge Pro 上的计算网络。
- 验证模块是否可以访问已启用的网络接口。

在本操作指南文章中，你将使用 web 服务器应用模块来演示该方案。

## <a name="prerequisites"></a>必备条件

在开始之前，需要：

- Azure Stack Edge Pro 设备。 请确保：

    - 计算网络设置在设备上进行配置。
    - 根据 [教程：激活你的设备](azure-stack-edge-gpu-deploy-activate.md)中的步骤激活设备。
- 已根据教程完成 **配置计算** 步骤：在设备上的 [Azure Stack Edge Pro 设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md) 。 设备应有关联的 IoT 中心资源、IoT 设备和 IoT Edge 设备。


## <a name="add-webserver-app-module"></a>添加 web 服务器应用模块

执行以下步骤，将 web 服务器应用模块添加到 Azure Stack Edge Pro 设备上。

1. 在与设备关联的 IoT 中心资源中，请参阅 **自动设备管理 > IoT Edge**。
1. 选择并单击与 Azure Stack Edge Pro 设备关联的 IoT Edge 设备。 

    ![选择 IoT Edge 设备](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. 选择“设置模块”。 在 **设备上的 "设置模块**" 上，选择 " **+ 添加** "，然后选择 " **IoT Edge 模块**"。

    ![选择 IoT Edge 模块](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. 在 " **添加 IoT Edge" 模块**中：

    1. 指定想要部署的 web 服务器应用模块的 **名称** 。
    2. 在 " **模块设置** " 选项卡下，提供模块映像的 **图像 URI** 。 检索与所提供的名称和标记相匹配的模块。 在这种情况下， `nginx:stable` 将从公共 [Docker 存储库](https://hub.docker.com/_/nginx/)中提取 (标记为稳定) 的稳定 nginx 映像。

        ![添加 IoT Edge 模块](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. 在 **容器 "创建选项** " 选项卡中，粘贴下面的示例代码：  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        此配置允许你使用 http 端口8080上的 *http* 上的计算网络 IP 来访问该模块，默认的 web 服务器端口为 80)  (。 选择“添加”  。

        ![在 IoT Edge 自定义模块边栏选项卡中指定端口信息](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. 选择“查看 + 创建”  。 查看模块详细信息，然后选择 " **创建**"。

## <a name="verify-module-access"></a>验证模块访问

1. 验证模块是否已成功部署并且正在运行。 在 " **模块** " 选项卡上，模块的运行时状态应为 " **正在运行**"。  

    ![验证模块状态是否正在运行](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. 若要获取 web 服务器应用的外部终结点，请 [访问 Kubernetes 仪表板](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)。 
1. 在仪表板的左侧窗格中，按 **iotedge** 命名空间进行筛选。 请参阅 **发现和负载平衡 > 服务**。 在列出的服务列表下，找到 web 服务器应用模块的外部终结点。 

    ![连接到外部终结点上的 web 服务器应用](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. 选择外部端点以打开新的浏览器窗口。

    应该会看到 web 服务器应用正在运行。

    ![验证通过指定端口连接到模块](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>后续步骤

- 了解如何通过 IoT Edge 模块公开有状态应用程序<!--insert link-->.
