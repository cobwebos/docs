---
title: 教程：在 Azure 门户中使用 GPU 连接、配置和激活 Azure Stack Edge Pro 设备 | Microsoft Docs
description: 本教程介绍 Azure Stack Edge Pro GPU 部署方法，指导你连接、设置和激活物理设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 1f86b0fc847ade3153c8eaddb0d82bd968913b46
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90899629"
---
# <a name="tutorial-configure-device-settings-for-azure-stack-edge-pro-with-gpu"></a>教程：使用 GPU 配置 Azure Stack Edge Pro 的设备设置

本教程介绍如何使用板载 GPU 为 Azure Stack Edge Pro 设备配置设备相关设置。 可以通过本地 Web UI 设置设备名称、更新服务器和时间服务器。

设备设置可能需要大约 5-7 分钟才能完成。

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 配置设备设置
> * 配置更新 
> * 配置时间

## <a name="prerequisites"></a>先决条件

在 Azure Stack Edge Pro 设备上使用 GPU 配置设备相关设置之前，请确保：

* 对于物理设备：

    - 已根据[安装 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) 中详述的内容安装了物理设备。
    - 已根据[教程：使用 GPU 为 Azure Stack Edge Pro 配置网络](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)中详述的内容在设备上配置了网络，同时启用并配置了计算网络。


## <a name="configure-device-settings"></a>配置设备设置

按照以下步骤配置设备相关设置：

1. 在“设备”页上执行以下步骤：

    1. 为设备输入一个易记名称。 易记名称必须包含 1 到 13 个字符，可以包含字母、数字和连字符。

    2. 为设备提供 DNS 域。 此域用于将设备设置为文件服务器。

    3. 若要验证并应用已配置的设备设置，请选择“应用”。

        ![本地 Web UI“设备”页 1](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        如果更改了设备名称和 DNS 域，则设备上自动生成的自签名证书将不起作用。 配置证书时，需要选择以下选项之一： 
        
        - 生成并下载设备证书。 
        - 为设备引入你自己的证书，包括签名链。
    

        ![本地 Web UI“设备”页 2](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. 更改设备名称和 DNS 域后，将创建 SMB 终结点。  

    5. 应用设置后，选择“下一步:更新服务器”。

        ![本地 Web UI“设备”页](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>配置更新

1. 在“更新”页上，现在可以配置设备更新的下载位置。  

    - 可以直接从“Microsoft 更新服务器”获取更新。

        ![本地 Web UI“更新服务器”页](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        也可以选择从“Windows Server Update Services”(WSUS) 部署更新。 提供 WSUS 服务器的路径。
        
        ![本地 Web UI“更新服务器”页](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > 如果配置了单独的 Windows 更新服务器，并且选择通过 https（而不是 http）进行连接，则需要提供连接到更新服务器所需的签名链证书。 有关如何创建和上传证书的信息，请转到[管理证书](azure-stack-edge-j-series-manage-certificates.md)。 

2. 选择“应用”。 
3. 配置更新服务器后，选择“下一步:时间”。
    

## <a name="configure-time"></a>配置时间

按照以下步骤在设备上配置时间设置。 

> [!IMPORTANT]
> 尽管时间设置是可选的，但强烈建议在本地网络上为设备配置主要 NTP 服务器和辅助 NTP 服务器。 如果本地服务器不可用，则可以配置公共 NTP 服务器。

NTP 服务器是必需的，因为设备必须同步时间，才能通过云服务提供程序进行身份验证。

1. 在“时间”页上，可以为设备选择时区以及主要和辅助 NTP 服务器。  
    
    1. 在“时区”下拉列表中选择时区，使之与部署设备的地理位置相对应。
        设备的默认时区为太平洋标准时间。 设备将此时区用于所有计划操作。

    2. 在“主 NTP 服务器”框中输入设备的主服务器，或者接受默认值：time.windows.com。  
        确保网络允许 NTP 流量从数据中心传递到 Internet。

    3. （可选）在“辅助 NTP 服务器”框中，为设备输入辅助服务器。

    4. 若要验证并应用配置的时间设置，请选择“应用”  。

        ![本地 Web UI“时间”页](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

2. 应用设置后，选择“下一步:证书”。


## <a name="next-steps"></a>后续步骤

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 配置设备设置
> * 配置更新 
> * 配置时间

若要了解如何为 Azure Stack Edge Pro 设备配置证书，请参阅：

> [!div class="nextstepaction"]
> [配置证书](./azure-stack-edge-gpu-deploy-configure-certificates.md)
