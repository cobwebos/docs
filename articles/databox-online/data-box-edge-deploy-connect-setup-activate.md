---
title: 在 Azure 门户中连接到、配置和激活 Azure Data Box Edge | Microsoft Docs
description: 本教程指导你连接、设置和激活物理设备，是介绍 Data Box Edge 部署方法的第三个教程。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3d6742bec54e612b5dca6d9ef6c4f67a33929448
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166570"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>教程：连接、设置、激活 Azure Data Box Edge（预览版） 

本教程介绍如何使用本地 Web UI 连接到 Data Box Edge 设备并设置和激活它。 

完成设置和激活过程可能需要大约 20 分钟。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 连接到物理设备
> * 设置和激活物理设备

> [!IMPORTANT]
> Data Box Edge 以预览版提供。 在订购和部署此解决方案之前，请查看 [Azure 预览版服务的条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 


## <a name="prerequisites"></a>先决条件

配置和设置 Data Box Edge 之前，请确保：

* 已根据[安装 Data Box Edge](data-box-edge-deploy-install.md) 中的详细内容安装了物理设备。
* 拥有所创建的用于管理 Data Box Edge 设备的 Data Box Edge 服务的激活密钥。 有关详细信息，请转到[准备部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)。

## <a name="connect-to-the-local-web-ui-setup"></a>连接到本地 Web UI 设置 

1. 在使用的计算机上配置以太网适配器以通过静态 IP 地址 192.168.100.5 和子网 255.255.255.0 连接到 Edge 设备。
2. 将计算机连接到设备上的端口 1。 
3. 打开浏览器窗口并通过 https://192.168.100.10 访问设备的本地 Web UI。 打开设备后，此操作可能需要几分钟完成。 
4. 此时会出现一个错误或警告，指出网站的安全证书有问题。 单击“继续访问此网页”。 （这些步骤可能因使用的浏览器而异。）
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. 登录到设备的 Web UI。 默认密码为 *Password1*。 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. 系统会提示你更改设备管理员密码。 键入一个包含 8 到 16 个字符的新密码。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。

现在位于设备的仪表板中。

## <a name="set-up-and-activate-the-physical-device"></a>设置和激活物理设备
 
1. 从仪表板中，可以转到配置物理设备并将其注册到 Data Box Edge 服务所需的各种设置。 “设备名”、“网络设置”、“Web 代理设置”和“时间设置”为可选设置。 只有“云设置”是必需设置。
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. 在“设备名称”页面上，配置设备的友好名称。 友好名称长度为 1 到 15 个字符，并可包含字母、数字和连字符。

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. （可选）配置**网络设置**。 在物理设备上，会看到六个网络接口。 端口 1 和端口 2 是 1 Gbps 网络接口。 端口 3、4 端口、端口 5 和端口 6 均为 25 Gbps 网络接口。 系统自动将端口 1 配置为仅用于管理的端口，而端口 2 到端口 6 都是数据端口。 网络设置页如下所示。
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    配置网络设置时，请记住：

    - 如果你的环境中启用了 DHCP，则会自动配置网络接口。 系统会自动分配 IP 地址、子网、网关和 DNS。
    - 如果未启用 DHCP，则可以根据需要分配静态 IP。
    - 可以将网络接口配置为 IPv4。
   
4. （可选）配置 Web 代理服务器。 尽管这一配置为可选操作，但如果使用 Web 代理，那么你只能在此处进行配置。
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   在“Web 代理”页中：
   
   1. 按此格式提供 Web 代理 URL：`http://host-IP address or FDQN:Port number`。 不支持 HTTPS URL。
   2. 将“身份验证”指定为“基本”或“无”。
   3. 如果使用身份验证，则还需提供“用户名”和“密码”。
   4. 单击“应用”验证并应用配置的 Web 代理设置。

5. （可选）配置设备的时间设置，例如时区以及主 NTP 服务器和辅助 NTP 服务器。 NTP 服务器是必需的，因为设备必须同步时间，才能通过云服务提供程序进行身份验证。
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    在“时间设置”页中：
    
    1. 根据部署设备的地理位置，从下拉列表中选择相应的“时区” 。 设备的默认时区为太平洋标准时间。 设备将此时区用于所有计划操作。
    2. 为设备指定“主 NTP 服务器”，或者接受默认值：time.windows.com。 确保网络允许 NTP 流量从数据中心传递到 Internet。
    3. （可选）为设备指定“辅助 NTP 服务器”。
    4. 单击“应用”验证并应用配置的时间设置。

6. 在“云设置”页面中，通过 Azure 门户中的 Data Box Edge 服务激活设备。
    
    1. 为 Data Box Edge 输入你在[获取激活密钥](data-box-edge-deploy-prep.md#get-the-activation-key)中获取的激活密钥。

    2. 单击“应用”。 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. 成功激活设备后，你会看到连接模式选项。 如果需要在部分断开连接或断开连接模式中使用设备，需配置这些设置。 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

已完成设备设置。 现在可以在设备上添加共享。


## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解有关 Data Box Edge 的主题，例如：

> [!div class="checklist"]
> * 连接到物理设备
> * 设置和激活物理设备


请继续学习下一个教程，了解如何使用 Data Box Edge 传输数据。

> [!div class="nextstepaction"]
> [使用 Data Box Edge 传输数据](./data-box-edge-deploy-add-shares.md)。