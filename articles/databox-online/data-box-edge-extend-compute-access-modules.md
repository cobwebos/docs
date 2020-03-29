---
title: 在 Azure 数据框边缘管理计算网络以访问模块*微软文档
description: 描述如何扩展数据框边缘的计算网络，以便通过外部 IP 访问模块。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "65917230"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>在 Azure 数据框边缘启用计算网络

本文介绍在 Azure 数据框边缘上运行的模块如何访问设备上启用的计算网络。

要配置网络，您需要执行以下步骤：

- 在数据盒边缘设备上启用网络接口进行计算
- 添加模块以访问数据框边缘的计算网络
- 验证模块可以访问启用的网络接口

在本教程中，您将使用 Webserver 应用模块来演示该方案。

## <a name="prerequisites"></a>先决条件

开始之前，您需要：

- 已完成设备设置的数据框边缘设备。
- 您已完成按照教程**配置计算**步骤[：在设备上使用 Azure 数据框边缘转换数据](data-box-edge-deploy-configure-compute-advanced.md#configure-compute)。 您的设备应具有关联的 IoT 中心资源、IoT 设备和 IoT 边缘设备。

## <a name="enable-network-interface-for-compute"></a>启用用于计算的网络接口

要通过外部网络访问设备上运行的模块，您需要将 IP 地址分配给设备上的网络接口。 可以从本地 Web UI 管理这些计算设置。

在本地 Web UI 上执行下述步骤，以便配置计算设置。

1. 在本地 Web UI 中，转到“配置”>“计算设置”。****  

2. **启用**要用于连接到要在设备上运行的计算模块的网络接口。

    - 如果使用静态 IP 地址，请为网络接口输入一个 IP 地址。
    - 如果使用 DHCP，则会自动分配 IP 地址。 此示例使用 DHCP。

    ![启用计算设置 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. 选择“应用”以应用设置。**** 如果使用 DHCP，请注意分配给网络接口的 IP 地址。

    ![启用计算设置](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>添加 Web 服务器应用模块

执行以下步骤，在数据盒边缘设备上添加 Web 服务器应用模块。

1. 转到与数据框边缘设备关联的 IoT 中心资源，然后选择**IoT 边缘设备**。
2. 选择与数据框边缘设备关联的 IoT 边缘设备。 在**设备详细信息**上，选择 **"设置模块**"。 在**添加模块**时，选择 **+添加**，然后选择**IoT 边缘模块**。
3. 在**IoT 边缘自定义模块**边边边边边边选项卡中：

    1. 为要部署的 Web 服务器应用模块指定**名称**。
    2. 为您的模块映像提供**图像 URI。** 检索与提供的名称和标记匹配的模块。 在这种情况下，`nginx:stable`将从公共[Docker 存储库](https://hub.docker.com/_/nginx/)中提取一个稳定的 nginx 映像（标记为稳定）。
    3. 在**容器创建选项**中，粘贴以下示例代码：  

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

        此配置允许您在 TCP 端口 8080 上通过*http（* 默认 Web 服务器端口为 80）使用计算网络 IP 访问模块。

        ![在 IoT 边缘自定义模块边选项卡中指定端口信息](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. 选择“保存”。****

## <a name="verify-module-access"></a>验证模块访问

1. 验证模块已成功部署且正在运行。 在"**设备详细信息**"页上，在 **"模块"** 选项卡上，模块的运行时状态应**运行**。  
2. 连接到 Web 服务器应用模块。 打开浏览器窗口并键入：

    `http://<compute-network-IP-address>:8080`

    您应该看到 Webserver 应用正在运行。

    ![通过指定端口验证与模块的连接](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure 门户管理用户](data-box-edge-manage-users.md)。
