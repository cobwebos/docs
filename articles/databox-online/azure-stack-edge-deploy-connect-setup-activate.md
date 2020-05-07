---
title: 教程：在 Azure 门户中连接、配置和激活 Azure Stack Edge 设备 | Microsoft Docs
description: 本教程介绍 Azure Stack Edge 部署方法，指导你连接、设置和激活物理设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 7c8a35b2699035b3ce4f96a94ca970da2cf343c4
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564425"
---
# <a name="tutorial-connect-set-up-and-activate-azure-stack-edge"></a>教程：连接、设置和激活 Azure Stack Edge 

本教程介绍如何使用本地 Web UI 连接到 Azure Stack Edge 设备并设置和激活它。

完成设置和激活过程可能需要大约 20 分钟。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 连接到物理设备
> * 设置和激活物理设备

## <a name="prerequisites"></a>先决条件

配置和设置 Azure Stack Edge 设备之前，请确保：

* 已根据[安装 Azure Stack Edge](azure-stack-edge-deploy-install.md) 中详述的内容安装了物理设备。
* 已从所创建的用于管理 Azure Stack Edge 设备的 Azure Stack Edge 服务获得激活密钥。 有关详细信息，请转到[准备部署 Azure Stack Edge](azure-stack-edge-deploy-prep.md)。

## <a name="connect-to-the-local-web-ui-setup"></a>连接到本地 Web UI 设置 

1. 在计算机上配置以太网适配器，以便通过静态 IP 地址 192.168.100.5 和子网 255.255.255.0 连接到 Azure Stack Edge 设备。

2. 将计算机连接到设备上的端口 1。 使用下图标识设备上的端口 1。

    ![已布线设备的背板](./media/azure-stack-edge-deploy-install/backplane-cabled.png)


3. 打开浏览器窗口并通过 `https://192.168.100.10` 访问设备的本地 Web UI。  
    打开设备后，此操作可能需要几分钟完成。 

    此时会出现一个错误或警告，指出网站的安全证书有问题。 
   
    ![网站安全证书错误消息](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. 选择“继续访问此网页”。   
    这些步骤可能因所用浏览器而异。

5. 登录到设备的 Web UI。 默认密码为 *Password1*。 
   
    ![Azure Stack Edge 设备登录页面](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. 出现提示时，请更改设备管理员密码。  
    新的密码必须包含 8 到 16 个字符。 它必须包含以下字符中的三项：大写字母、小写字母、数字和特殊字符。

现在位于设备的仪表板中。

## <a name="set-up-and-activate-the-physical-device"></a>设置和激活物理设备
 
仪表板显示的各种设置是配置物理设备并将其注册到 Azure Stack Edge 服务所需的。 “设备名”、“网络设置”、“Web 代理设置”和“时间设置”为可选设置     。 只有“云设置”是必需设置  。
   
![本地 Web UI“仪表板”页](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. 在左窗格中选择“设备名称”  ，然后输入设备的友好名称。  
    友好名称必须包含 1 到 15 个字符，并且包含字母、数字和连字符。

    ![本地 Web UI“设备名”页](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. （可选）在左窗格中选择“网络设置”  ，然后配置设置。  
    在物理设备上有六个网络接口。 端口 1 和端口 2 是 1 Gbps 网络接口。 端口 3、端口 4、端口 5 和端口 6 都是 25 Gbps 网络接口，也可用作 10 Gbps 网络接口。 系统自动将端口 1 配置为仅用于管理的端口，而端口 2 到端口 6 都是数据端口。 “网络设置”页如下所示  。
    
    ![本地 Web UI“网络设置”页](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    配置网络设置时，请记住：

   - 如果你的环境中启用了 DHCP，则会自动配置网络接口。 系统会自动分配 IP 地址、子网、网关和 DNS。
   - 如果未启用 DHCP，则可以根据需要分配静态 IP。
   - 可以将网络接口配置为 IPv4。

     >[!NOTE] 
     > 建议不要将网络接口的本地 IP 地址从静态切换到 DCHP，除非有另一个 IP 地址连接到该设备。 如果使用一个网络接口并切换到 DHCP，则无法确定 DHCP 地址。 如果要更改为 DHCP 地址，请等待设备注册服务后再更改。 然后，可以在 Azure 门户中为你的服务查看“设备属性”中所有适配器的 IP  。

3. （可选）在左窗格中选择“Web 代理设置”  ，然后配置 Web 代理服务器。 尽管这一配置为可选操作，但如果使用 Web 代理，则只能在此页上进行配置。
   
   ![本地 Web UI“Web 代理设置”页](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   在“Web 代理设置”  页上，执行以下操作：
   
   a. 在“Web 代理 URL”框中，按以下格式输入 URL：`http://host-IP address or FQDN:Port number` 。 不支持 HTTPS URL。

   b. 在“身份验证”下选择“无”或“NTLM”。   

   c. 如果使用身份验证，请输入用户名和密码。

   d. 若要验证并应用配置的 Web 代理设置，请选择“应用设置”  。
   
   > [!NOTE]
   > 不支持代理自动配置 (PAC) 文件。 PAC 文件定义了 Web 浏览器和其他用户代理如何自动选择适当的代理服务器（访问方法）来获取给定 URL。
   > 尝试拦截和读取所有流量（然后用其自己的证书对所有内容进行重新签名）的代理不兼容，因为代理的证书不受信任。
   > 通常，透明代理非常适用于 Azure Stack Edge。

4. （可选）在左窗格中选择“时间设置”，然后为设备配置时区以及主 NTP 服务器和辅助 NTP 服务器。   
    NTP 服务器是必需的，因为设备必须同步时间，才能通过云服务提供程序进行身份验证。
       
    在“时间设置”  页上，执行以下操作：
    
    1. 在“时区”下拉列表中选择时区，使之与部署设备的地理位置相对应。 
        设备的默认时区为太平洋标准时间。 设备将此时区用于所有计划操作。

    2. 在“主 NTP 服务器”框中输入设备的主服务器，或者接受默认值：time.windows.com。   
        确保网络允许 NTP 流量从数据中心传递到 Internet。

    3. （可选）在“辅助 NTP 服务器”框中，为设备输入辅助服务器。 

    4. 若要验证并应用配置的时间设置，请选择“应用设置”  。

        ![本地 Web UI“时间设置”页](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. （可选）在左窗格中，选择“存储设置”以配置设备上的存储复原能力  。 此功能目前处于预览状态。 默认情况下，设备上的存储不可复原，如果设备上的数据磁盘出现故障，则会导致数据丢失。 启用可复原选项后，将重新配置设备上的存储，并且设备可以承受一个数据磁盘的故障而不会丢失数据。 将存储配置为可复原将减少设备的可用容量。

    > [!IMPORTANT] 
    > 只能在激活设备之前配置复原能力。 

    ![本地 Web UI“存储设置”页](./media/azure-stack-edge-deploy-connect-setup-activate/storage-settings.png)

6. 在左窗格中选择“云设置”，然后通过 Azure 门户中的 Azure Stack Edge 服务激活设备  。
    
    1. 在“激活密钥”框中，为 Azure Stack Edge 输入你在[获取激活密钥](azure-stack-edge-deploy-prep.md#get-the-activation-key)中获得的激活密钥  。
    2. 选择“应用”。 
       
        ![本地 Web UI“云设置”页](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. 首先激活设备。 然后扫描设备以查找任何关键更新，如果可用，则自动应用更新。 你可看到有关此影响的通知。

        该对话框还具有恢复密钥，应将其复制并保存在安全的位置。 此密钥用于在设备无法启动时恢复数据。

        ![更新本地 Web UI“云设置”页](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. 更新成功完成后，可能需要等待几分钟。 页面更新以指示设备已成功激活。

        ![更新本地 Web UI“云设置”页](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-8.png)

已完成设备设置。 现在可以在设备上添加共享。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 连接到物理设备
> * 设置和激活物理设备

若要了解如何使用 Azure Stack Edge 设备传输数据，请参阅：

> [!div class="nextstepaction"]
> [使用 Azure Stack Edge 传输数据](./azure-stack-edge-deploy-add-shares.md)
