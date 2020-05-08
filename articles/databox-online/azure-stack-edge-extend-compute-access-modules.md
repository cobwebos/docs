---
title: 在 Azure Stack Edge 上管理计算网络以访问模块 |Microsoft Docs
description: 描述如何在 Azure Stack 边缘扩展计算网络，以通过外部 IP 访问模块。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: b352458b768c997f295596a7cbd5e8c02f843a9f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569792"
---
# <a name="enable-compute-network-on-your-azure-stack-edge"></a>在 Azure Stack 边缘启用计算网络

本文介绍 Azure Stack 边缘上运行的模块如何访问在设备上启用的计算网络。

若要配置网络，请执行以下步骤：

- 启用 Azure Stack Edge 设备上的网络接口进行计算
- 添加模块以访问 Azure Stack 边缘上的计算网络
- 验证模块是否可以访问已启用的网络接口

在本教程中，你将使用 web 服务器应用模块来演示该方案。

## <a name="prerequisites"></a>先决条件

在开始之前，需要：

- 已完成设备安装的 Azure Stack Edge 设备。
- 根据本教程中所述完成了 "**计算**步骤：在设备上将[数据与 Azure Stack 边缘转换](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute)"。 设备应有关联的 IoT 中心资源、IoT 设备和 IoT Edge 设备。

## <a name="enable-network-interface-for-compute"></a>为计算启用网络接口

若要通过外部网络访问设备上运行的模块，需要为设备上的网络接口分配一个 IP 地址。 可以从本地 Web UI 管理这些计算设置。

在本地 Web UI 上执行下述步骤，以便配置计算设置。

1. 在本地 Web UI 中，转到“配置”>“计算设置”。****  

2. **启用**要用于连接到要在设备上运行的计算模块的网络接口。

    - 如果使用静态 IP 地址，请为网络接口输入一个 IP 地址。
    - 如果使用 DHCP，将自动分配 IP 地址。 此示例使用 DHCP。

    ![启用计算设置1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. 选择“应用”以应用设置。**** 如果使用 DHCP，请记下分配给网络接口的 IP 地址。

    ![启用计算设置](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>添加 web 服务器应用模块

执行以下步骤，将 web 服务器应用模块添加到 Azure Stack 边缘设备上。

1. 中转到与 Azure Stack Edge 设备关联的 IoT 中心资源，然后选择 " **IoT Edge 设备**"。
2. 选择与 Azure Stack 边缘设备关联的 IoT Edge 设备。 在 "**设备详细信息**" 中，选择 "**设置模块**"。 在 "**添加模块**" 中，选择 " **+ 添加**"，然后选择 " **IoT Edge 模块**"。
3. 在 " **IoT Edge 自定义模块**" 边栏选项卡中：

    1. 指定想要部署的 web 服务器应用模块的**名称**。
    2. 提供模块映像的**图像 URI** 。 检索与所提供的名称和标记相匹配的模块。 在这种情况`nginx:stable`下，将从公共[Docker 存储库](https://hub.docker.com/_/nginx/)拉取稳定的 nginx 映像（标记为稳定）。
    3. 在**容器 "创建选项**" 中，粘贴以下代码示例：  

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

        此配置允许你使用 http 端口8080上的*http*上的计算网络 IP （默认 web 服务器端口为80）访问该模块。

        ![在 IoT Edge 自定义模块边栏选项卡中指定端口信息](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. 选择“保存”  。

## <a name="verify-module-access"></a>验证模块访问

1. 验证模块是否已成功部署并且正在运行。 在 "**设备详细信息**" 页上的 "**模块**" 选项卡上，模块的运行时状态应为 "**正在运行**"。  
2. 连接到 web 服务器应用模块。 打开浏览器窗口并键入：

    `http://<compute-network-IP-address>:8080`

    应该会看到 web 服务器应用正在运行。

    ![验证通过指定端口连接到模块](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure 门户管理用户](azure-stack-edge-manage-users.md)。
