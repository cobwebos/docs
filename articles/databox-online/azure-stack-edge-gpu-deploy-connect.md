---
title: 教程：在 Azure 门户中使用 GPU 连接、配置和激活 Azure Stack Edge Pro 设备 | Microsoft Docs
description: 了解如何通过本地 Web UI 使用板载 GPU 连接到 Azure Stack Edge 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: f069f711ccf5672041ea6f3b15a52fa2b3ba2022
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900031"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-with-gpu"></a>教程：使用 GPU 连接到 Azure Stack Edge Pro

本教程介绍如何通过本地 Web UI 使用板载 GPU 连接到 Azure Stack Edge Pro 设备。

连接过程可能需要大约 5 分钟才能完成。

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 连接到物理设备


## <a name="prerequisites"></a>先决条件

在使用 GPU 配置和设置 Azure Stack Edge Pro 设备之前，请确保：

* 已根据[安装 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) 中详述的内容安装了物理设备。


## <a name="connect-to-the-local-web-ui-setup"></a>连接到本地 Web UI 设置

1. 在计算机上配置以太网适配器，以便通过静态 IP 地址 192.168.100.5 和子网 255.255.255.0 连接到 Azure Stack Edge Pro 设备。

2. 将计算机连接到设备上的端口 1。 如果将计算机直接连接到设备（不通过交换机），请使用交叉电缆或 USB 以太网适配器。 使用下图标识设备上的端口 1。

    ![已布线设备的背板](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    设备的背板看上去可能略有不同，具体取决于你收到的确切型号。 有关详细信息，请参阅[连接设备电缆](azure-stack-edge-gpu-deploy-install.md#cable-the-device)。


3. 打开浏览器窗口并通过 `https://192.168.100.10` 访问设备的本地 Web UI。  
    打开设备后，此操作可能需要几分钟完成。

    此时会出现一个错误或警告，指出网站的安全证书有问题。 
   
    ![网站安全证书错误消息](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. 选择“继续访问此网页”。  
    这些步骤可能因所用浏览器而异。

5. 登录到设备的 Web UI。 默认密码为 *Password1*。 
   
    ![Azure Stack Edge Pro 设备登录页面](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. 出现提示时，请更改设备管理员密码。  
    新的密码必须包含 8 到 16 个字符。 它必须包含以下字符中的三项：大写字母、小写字母、数字和特殊字符。

你现在位于设备的“概述”页面。 下一步是配置设备的网络设置。


## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]
> * 先决条件
> * 连接到物理设备


若要了解如何在 Azure Stack Edge Pro 设备上配置网络设置，请参阅：

> [!div class="nextstepaction"]
> [配置网络](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
