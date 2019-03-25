---
title: 在 Azure 门户中连接到、配置和激活 Azure Data Box Gateway | Microsoft Docs
description: 此用于部署 Data Box Gateway 的第三个教程将指导你连接、设置和激活虚拟设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: de2ef4908260a62acf28a270dda6ad738a1760b9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402318"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>教程：连接、 设置、 激活 Azure 数据框网关

## <a name="introduction"></a>简介

本教程介绍如何连接到、 设置，并使用本地 web UI 激活你的数据框网关设备。 

完成设置和激活过程可能需要大约 10 分钟。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 连接到虚拟设备
> * 设置和激活虚拟设备

## <a name="prerequisites"></a>必备组件

配置和设置 Data Box Gateway 之前，请确保：

* 已预配虚拟设备并获取指向它的连接的 URL 中所述[预配在 HYPER-V 中的数据框的网关](data-box-gateway-deploy-provision-hyperv.md)或[预配 VMware 中的数据框网关](data-box-gateway-deploy-provision-vmware.md)。
* 拥有所创建的用于管理 Data Box Gateway 设备的 Data Box Gateway 服务的激活密钥。 有关详细信息，请转到[准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。


## <a name="connect-to-the-local-web-ui-setup"></a>连接到本地 Web UI 设置 

1. 打开浏览器窗口和访问本地 web UI 上的设备：
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   使用在上一教程中记下的连接 URL。 此时会出现一个错误或警告，指出网站的安全证书有问题。

2. 选择“继续访问此网页”。 这些步骤可能因所用浏览器而异。
   
    ![网站安全证书错误消息](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. 登录到虚拟设备的 Web UI。 默认密码为 *Password1*。 
   
    ![登录到本地 web UI](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. 在提示符下，更改设备密码。 新密码必须包含 8 到 16 个字符之间。 它必须包含的以下 3 种： 大写字母、 小写字母、 数字和特殊字符。

    ![更改设备密码](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

现在位于设备的仪表板中。

## <a name="set-up-and-activate-the-virtual-device"></a>设置和激活虚拟设备
 
你的仪表板显示配置和虚拟设备注册到数据框网关服务所需的各种设置。 “设备名”、“网络设置”、“Web 代理设置”和“时间设置”为可选设置。 只有“云设置”是必需设置。
   
![本地 web UI"仪表板"页](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. 在左窗格中，选择**设备名称**，然后输入你的设备的友好名称。 友好名称必须包含 1 到 15 个字符，包含字母、 数字和连字符。

    ![本地 web UI"设备名称"页](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. （可选）在左窗格中，选择**网络设置**，然后配置设置。 在虚拟设备，你将看到至少一个网络接口，并根据基础虚拟机中配置多少的详细信息。 启用了一个网络接口的虚拟设备的“网络设置”页面如下所示。
    
    ![本地 web UI"网络设置"页](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    配置网络设置时，请记住：

    - 如果你的环境中启用了 DHCP，则会自动配置网络接口。 系统会自动分配 IP 地址、子网、网关和 DNS。
    - 如果未启用 DHCP，则可以根据需要分配静态 IP。
    - 可以将网络接口配置为 IPv4。

     >[!NOTE] 
     > 建议不要将网络接口的本地 IP 地址从静态切换到 DHCP，除非有另一个 IP 地址连接到该设备。 如果使用一个网络接口并切换到 DHCP，则无法确定 DHCP 地址。 如果要更改为 DHCP 地址，请等待设备注册服务后再更改。 然后，可以在 Azure 门户中为你的服务查看“设备属性”中所有适配器的 IP。

3. （可选）配置 Web 代理服务器。 尽管 web 代理配置是可选的如果您使用 web 代理，可以仅在此页上对其进行配置。
   
   ![本地 web UI"Web 代理设置"页](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   上**Web 代理**页上，执行以下操作：
   
   1. 在“Web 代理 URL”框中，按以下格式输入 URL：`http://&lt;host-IP address or FQDN&gt;:Port number`。 不支持 HTTPS URL。
   2. 在“身份验证”下选择“无”或“NTLM”。
   3. 如果使用的身份验证，请输入**用户名**并**密码**。
   4. 若要验证并应用配置的 web 代理设置，请选择**应用**。

4. （可选）在左窗格中选择“时间设置”，然后为设备配置时区以及主 NTP 服务器和辅助 NTP 服务器。 

    NTP 服务器是必需的，因为设备必须同步时间，才能通过云服务提供程序进行身份验证。
    
    ![本地 web UI"时间设置"页](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    在中**时间设置**页上，执行以下操作：
    
    1. 在中**时区**下拉列表中，选择对应于在其中部署设备的地理位置的时区。
        设备的默认时区为太平洋标准时间。 设备将此时区用于所有计划操作。

    2. 指定**主 NTP 服务器**为你的设备，或接受默认值的`time.windows.com`。   
        确保网络允许 NTP 流量从数据中心传递到 Internet。

    3. （可选） 在**辅助 NTP 服务器**框中，输入你的设备的辅助服务器。

    4. 若要验证并应用配置的时间设置，请选择“应用”。

6. 在左窗格中，选择**云设置**，并随后激活将设备与 Azure 门户中的数据框网关服务。
    
    1. 在中**激活密钥**框中，输入**激活密钥**获取[获取激活密钥](data-box-gateway-deploy-prep.md#get-the-activation-key)数据框网关。

    2. 选择“激活”。
       
         ![本地 web UI"云设置"页](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. 激活该设备和关键更新，如果可用，会自动应用。 请参阅该结果的通知。 监视通过 Azure 门户的更新进度。

        ![本地 web UI"云设置"页](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **该对话框还具有您应将其复制并保存在安全的位置的恢复密钥。此密钥用于恢复数据，以防设备无法启动。**


    4. 您可能需要等待几分钟时间才能成功完成的更新。 更新后已完成，请登录到设备。 **云设置**页上更新以指示设备是否已成功激活。

        ![更新本地 web UI"云设置"页](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

已完成设备设置。 现在可以在设备上添加共享。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 连接到虚拟设备
> * 设置和激活虚拟设备

若要了解如何使用数据框网关传输数据，请参阅：

> [!div class="nextstepaction"]
> [使用 Data Box Gateway 传输数据](./data-box-gateway-deploy-add-shares.md)。
