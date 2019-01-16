---
title: 在 Azure 门户中连接到、配置和激活 Azure Data Box Gateway | Microsoft Docs
description: 此用于部署 Data Box Gateway 的第三个教程将指导你连接、设置和激活虚拟设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 5e83eb46a4d62c6aaf0862d4bb1aa046c5d64dde
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120507"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>教程：连接、设置、激活 Azure Data Box Gateway（预览版） 

## <a name="introduction"></a>介绍

本教程介绍了如何使用本地 Web UI 连接到、设置以及激活 Data Box Gateway 设备。 

完成设置和激活过程可能需要大约 10 分钟。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 连接到虚拟设备
> * 设置和激活虚拟设备

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


> [!IMPORTANT]
> - Data Box Gateway 为预览版。 在订购和部署此解决方案之前，请查看 [Azure 预览版服务的条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 


## <a name="prerequisites"></a>先决条件

配置和设置 Data Box Gateway 之前，请确保：

* 已根据[在 Hyper-V 中预配 Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md) 或[在 VMware 中预配 Data Box Gateway](data-box-gateway-deploy-provision-vmware.md) 中的详细说明预配了虚拟设备并获取了它的连接 URL。
* 拥有所创建的用于管理 Data Box Gateway 设备的 Data Box Gateway 服务的激活密钥。 有关详细信息，请转到[准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。

<!--* If this is the second or subsequent virtual device that you are registering with an existing StorSimple Device Manager service, you should have the service data encryption key. This key was generated when the first device was successfully registered with this service. If you have lost this key, see [Get the service data encryption key](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) for your Data Box Gateway.-->

## <a name="connect-to-the-local-web-ui-setup"></a>连接到本地 Web UI 设置 

1. 打开浏览器窗口并连接到本地 Web UI。 键入：
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   使用在上一教程中记下的连接 URL。 此时会出现一个错误，指出网站的安全证书有问题。 单击“继续访问此网页”。 （这些步骤可能因使用的浏览器而异。）
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. 登录到虚拟设备的 Web UI。 默认密码为 *Password1*。 
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. 将会提示更改设备管理员密码。 键入一个包含 8 到 16 个字符的新密码。 该密码必须包含以下各项中的 3 项：大写字母、小写字母、数字和特殊字符。

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

你现在已处于设备的**仪表板**中。

## <a name="set-up-and-activate-the-virtual-device"></a>设置和激活虚拟设备
 
1. 从仪表板中，可以转到配置虚拟设备并将其注册到 Data Box Gateway 服务所需的各种设置。 “网络设置”、“Web 代理设置”和“时间设置”为可选设置。 只有“设备名称”和“云设置”是必需设置。
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. 在“设备名称”页面上，配置设备的友好名称。 友好名称长度为 1 到 15 个字符，并可包含字母、数字和连字符。

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. （可选）配置**网络设置**。 你将看到至少一个网络接口或更多，具体取决于你在基础虚拟机上配置了多少网络接口。 启用了一个网络接口的虚拟设备的“网络设置”页面如下所示。
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    配置网络设置时，请记住以下事项：

    - 如果你的环境中启用了 DHCP，则会自动配置网络接口。 因此，系统会自动分配 IP 地址、子网、网关和 DNS。
    - 如果未启用 DHCP，则你可以根据需要分配静态 IP。
    - 可以将网络接口配置为 IPv4。

    >[!NOTE] 
    > 建议你不要将网络接口的本地 IP 地址从静态切换到 DHCP，除非你有另一个 IP 地址连接到该设备。 如果使用一个网络接口并切换到 DHCP，则无法确定 DHCP 地址。 如果要更改为 DHCP 地址，请等待设备注册服务后再更改。 然后，可以在 Azure 门户中为你的服务查看“设备属性”中的所有适配器的 IP。

4. （可选）配置 Web 代理服务器。 尽管 Web 代理服务器配置是可选项，仍应注意，在使用 Web 代理时，只能在此处配置它。
   
   ![](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   在“Web 代理”页中：
   
   1. 以下述格式提供“Web 代理 URL”：*http://&lt;主机 IP 地址或 FDQN&gt;:端口号*。 请注意，不支持 HTTPS URL。
   2. 将“身份验证”指定为“基本”或“无”。
   3. 如果使用身份验证，则还需提供“用户名”和“密码”。
   4. 单击“应用”。 此时会验证并应用配置的 Web 代理设置。

5. （可选）配置设备的时间设置，例如时区以及主 NTP 服务器和辅助 NTP 服务器。 NTP 服务器是必需的，因为设备必须同步时间，才能通过云服务提供程序进行身份验证。
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    在“时间设置”页中：
    
    1. 根据部署设备的地理位置，从下拉列表中选择相应的“时区” 。 设备的默认时区为太平洋标准时间。 设备将此时区用于所有计划操作。
    2. 为设备指定“主 NTP 服务器”，或者接受默认值：time.windows.com。 确保网络允许 NTP 流量从数据中心传递到 Internet。
    3. （可选）为设备指定“辅助 NTP 服务器”。
    4. 单击“应用” 。 此时会验证并应用配置的时间设置。

6. 在“云设置”页面中，通过 Azure 门户中的 Data Box Gateway 服务激活设备。
    
    1. 为 Data Box Gateway 输入你在[获取激活密钥](data-box-gateway-deploy-prep.md#get-the-activation-key)中获取的**激活密钥**。

    2. 单击“激活”。 
       
         ![](./media/data-box-gateway-deploy-connect-setup-activate/image10.png)
    
    3. 可能需要等一会儿才能成功激活设备。 激活后，页面会更新以指示设备已成功激活。


## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解有关 Data Box Gateway 的主题，例如：

> [!div class="checklist"]
> * 连接到虚拟设备
> * 设置和激活虚拟设备


请继续学习下一教程，了解如何使用 Data Box Gateway 传输数据。

> [!div class="nextstepaction"]
> [使用 Data Box Gateway 传输数据](./data-box-gateway-deploy-add-shares.md)。
