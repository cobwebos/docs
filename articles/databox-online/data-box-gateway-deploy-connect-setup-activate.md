---
title: 在 Azure 门户中连接、配置和激活 Azure Data Box Gateway
description: 此用于部署 Data Box Gateway 的第三个教程将指导你连接、设置和激活虚拟设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 33333f8df1e4809a330815e34074d1bca556cd14
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561827"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>教程：连接、设置、激活 Azure Data Box Gateway

## <a name="introduction"></a>介绍

本教程介绍了如何通过使用本地 Web UI 连接到、设置以及激活 Data Box Gateway 设备。 

完成设置和激活过程可能需要大约 10 分钟。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 连接到虚拟设备
> * 设置和激活虚拟设备

## <a name="prerequisites"></a>必备条件

配置和设置 Data Box Gateway 之前，请确保：

* 已根据[在 Hyper-V 中预配 Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md) 或[在 VMware 中预配 Data Box Gateway](data-box-gateway-deploy-provision-vmware.md) 中的详细说明预配了虚拟设备并获取了它的连接 URL。
* 拥有所创建的用于管理 Data Box Gateway 设备的 Data Box Gateway 服务的激活密钥。 有关详细信息，请转到[准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。


## <a name="connect-to-the-local-web-ui-setup"></a>连接到本地 Web UI 设置 

1. 打开浏览器窗口并在以下网址访问设备的本地 Web UI：
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   使用在上一教程中记下的连接 URL。 此时会出现一个错误或警告，指出网站的安全证书有问题。

2. 选择“继续访问此网页”。  这些步骤可能因所用浏览器而异。
   
    ![网站安全证书错误消息](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. 登录到虚拟设备的 Web UI。 默认密码为 *Password1*。 
   
    ![登录到本地 Web UI](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. 出现提示时，请更改设备密码。 新的密码必须包含 8 到 16 个字符。 它必须包含以下各项中的 3 项：小写字母、大写字母、数字和特殊字符。

    ![更改设备密码](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

现在位于设备的仪表板中  。

## <a name="set-up-and-activate-the-virtual-device"></a>设置和激活虚拟设备
 
仪表板会显示配置虚拟设备并将其注册到 Data Box Gateway 服务所需的各种设置。 “设备名”、“网络设置”、“Web 代理设置”和“时间设置”为可选设置     。 只有“云设置”是必需设置  。
   
![本地 Web UI“仪表板”页](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. 在左窗格中选择“设备名称”，然后输入设备的友好名称  。 友好名称必须包含 1 到 15 个字符，并且必须包含字母、数字和连字符。

    ![本地 Web UI“设备名”页](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. （可选）在左窗格中选择“网络设置”，然后配置设置  。 在虚拟设备上，你将看到至少一个网络接口或更多接口，具体取决于你在基础虚拟机上配置的网络接口数。 启用了一个网络接口的虚拟设备的“网络设置”  页面如下所示。
    
    ![本地 Web UI“网络设置”页](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    配置网络设置时，请记住：

    - 如果你的环境中启用了 DHCP，则会自动配置网络接口。 系统会自动分配 IP 地址、子网、网关和 DNS。
    - 如果未启用 DHCP，则可以根据需要分配静态 IP。
    - 可以将网络接口配置为 IPv4。

     >[!NOTE] 
     > 建议不要将网络接口的本地 IP 地址从静态切换到 DHCP，除非有另一个 IP 地址连接到该设备。 如果使用一个网络接口并切换到 DHCP，则无法确定 DHCP 地址。 如果要更改为 DHCP 地址，请等待设备注册服务后再更改。 然后，可以在 Azure 门户中为你的服务查看“设备属性”中所有适配器的 IP  。

3. （可选）配置 Web 代理服务器。 尽管这一配置为可选操作，但如果使用 Web 代理，则只能在此页上进行配置。
   
   ![本地 Web UI“Web 代理设置”页](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   在“Web 代理”页上，执行以下操作  ：
   
   1. 在“Web 代理 URL”框中，按以下格式输入 URL：`http://&lt;host-IP address or FQDN&gt;:Port number`  。 不支持 HTTPS URL。
   2. 在“身份验证”下选择“无”或“NTLM”。   
   3. 如果使用身份验证，请输入用户名和密码   。
   4. 若要验证并应用配置的 Web 代理设置，请选择“应用”  。

4. （可选）在左窗格中选择“时间设置”，然后为设备配置时区以及主 NTP 服务器和辅助 NTP 服务器。  

    NTP 服务器是必需的，因为设备必须同步时间，才能通过云服务提供程序进行身份验证。
    
    ![本地 Web UI“时间设置”页](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    在“时间设置”页上，执行以下操作  ：
    
    1. 在“时区”下拉列表中选择时区，使之与部署设备的地理位置相对应  。
        设备的默认时区为太平洋标准时间。 设备将此时区用于所有计划操作。

    2. 为设备指定“主 NTP 服务器”，或者接受默认值：`time.windows.com`  。   
        确保网络允许 NTP 流量从数据中心传递到 Internet。

    3. （可选）在“辅助 NTP 服务器”框中，为设备输入辅助服务器  。

    4. 若要验证并应用配置的时间设置，请选择“应用”  。

6. 在左窗格中选择“云设置”，然后通过 Azure 门户中的 Data Box Gateway 服务激活设备  。
    
    1. 在“激活密钥”框中，为 Data Box Gateway 输入你在[获取激活密钥](data-box-gateway-deploy-prep.md#get-the-activation-key)中获取的激活密钥   。

    2. 选择“激活”  。
       
         ![本地 Web UI“云设置”页](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. 设备已激活，并且会自动应用关键更新（如果可用）。 你可看到有关此影响的通知。 通过 Azure 门户监视更新进度。

        ![本地 Web UI“云设置”页](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        该对话框还具有恢复密钥，应将其复制并保存在安全的位置。  此密钥用于在设备无法启动时恢复数据。


    4. 成功完成更新可能需要几分钟时间，请等待。 更新完成后，请登录到设备。 “云设置”页面更新以指示设备已成功激活  。

        ![更新本地 Web UI“云设置”页](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

已完成设备设置。 现在可以在设备上添加共享。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 连接到虚拟设备
> * 设置和激活虚拟设备

若要了解如何使用 Data Box Gateway 来传输数据，请参阅：

> [!div class="nextstepaction"]
> [使用 Data Box Gateway 传输数据](./data-box-gateway-deploy-add-shares.md)。
