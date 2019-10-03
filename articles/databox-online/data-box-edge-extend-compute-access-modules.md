---
title: 管理访问模块在 Azure 数据框边缘计算网络 |Microsoft Docs
description: 介绍如何扩展你的数据框边缘，以通过外部 IP 访问模块计算网络。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65917230"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>启用你的 Azure 数据框边缘上的计算网络

本文介绍如何在 Azure 数据框边缘上运行的模块可以访问计算网络在设备上启用。

若要配置的网络，您将执行以下步骤：

- 启用计算在数据框边缘设备上的网络接口
- 将模块添加到你的数据框边缘上访问计算网络
- 验证该模块可以访问已启用的网络接口

在本教程中，您将使用 web 服务器应用模块来演示方案。

## <a name="prerequisites"></a>必备组件

在开始之前，您将需要：

- 具有设备安装程序已完成的数据框边缘设备。
- 已完成**配置计算**为每个步骤[教程：对数据进行 Azure 数据框边缘](data-box-edge-deploy-configure-compute-advanced.md#configure-compute)在设备上。 你的设备应具有关联的 IoT 中心资源、 IoT 设备和 IoT Edge 设备。

## <a name="enable-network-interface-for-compute"></a>启用网络接口，用于计算

若要访问外部网络通过在设备上运行的模块，你将需要向你的设备上的网络接口分配 IP 地址。 可以从本地 Web UI 管理这些计算设置。

在本地 Web UI 上执行下述步骤，以便配置计算设置。

1. 在本地 Web UI 中，转到“配置”>“计算设置”。   

2. **启用**你想要用于连接到将在设备运行的计算模块的网络接口。

    - 如果使用静态 IP 地址，请为网络接口输入一个 IP 地址。
    - 如果使用 DHCP，自动分配 IP 地址。 此示例使用 DHCP。

    ![启用计算设置 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. 选择“应用”以应用设置。  记下分配给网络接口，如果使用 DHCP 的 IP 地址。

    ![启用计算设置](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>添加 web 服务器应用模块

执行以下步骤来在数据框边缘设备上添加 web 服务器应用程序模块。

1. 请转到与你的数据框边缘设备相关联的 IoT 中心资源，然后选择**IoT Edge 设备**。
2. 选择与你的数据框边缘设备相关联的 IoT Edge 设备。 上**设备详细信息**，选择**设置模块**。 上**将模块添加**，选择 **+ 添加**，然后选择**IoT Edge 模块**。
3. 在中**IoT Edge 的自定义模块**边栏选项卡：

    1. 指定**名称**为你想要部署的 web 服务器应用程序模块。
    2. 提供**映像 URI**模块映像。 检索与提供的名称和标记匹配的模块。 在这种情况下，`nginx:stable`将从公共拉取 （标记为稳定） 的稳定 nginx 映像[Docker 存储库](https://hub.docker.com/_/nginx/)。
    3. 在中**容器创建选项**，粘贴下面的示例代码：  

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

        此配置允许您访问该模块通过使用计算网络 IP *http*上 TCP 端口 8080 （与默认 web 服务器端口正在 80）。

        ![IoT Edge 的自定义模块边栏选项卡中指定的端口信息](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. 选择“保存”。 

## <a name="verify-module-access"></a>验证模块的访问权限

1. 验证已成功部署该模块正在运行。 上**设备详细信息**页上，在**模块**选项卡，该模块的运行时状态将变为**运行**。  
2. 连接到 web 服务器应用模块。 打开浏览器窗口并键入：

    `http://<compute-network-IP-address>:8080`

    您应该看到 web 服务器应用程序正在运行。

    ![通过指定端口验证连接到模块](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure 门户管理用户](data-box-edge-manage-users.md)。
