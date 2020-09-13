---
title: 使用 Azure 专用终结点以私密方式连接到 Web 应用
description: 本文介绍如何使用 Azure 专用终结点将专用连接到 Web 应用
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 09/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3d547546c3c0e0bbcdde65a654bf373ab7407be3
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569437"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>使用 Azure 专用终结点以私密方式连接到 Web 应用（预览版）

Azure 专用终结点是 Azure 中专用链接的构建基块。 它可以让你以私密方式连接到 Web 应用。
本快速入门介绍如何使用专用终结点部署一个 Web 应用，然后从虚拟机连接到此 Web 应用。

有关详细信息，请参阅 [使用 Azure Web 应用的专用终结点][privatenedpointwebapp]。

> [!Note]
>在公共区域中，针对高级 V2 Windows 和 Linux Web 应用以及弹性高级函数提供了此预览版。 

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="virtual-network-and-virtual-machine"></a>虚拟网络和虚拟机

在本部分，你将创建虚拟网络和子网来托管用于通过专用终结点访问 Web 应用的 VM。

### <a name="create-the-virtual-network"></a>创建虚拟网络

在本部分，请创建虚拟网络和子网。

1. 在屏幕的左上方选择“创建资源” > “网络” > “虚拟网络”，或者在搜索框中搜索“虚拟网络”。   

1. 在“创建虚拟网络”的“基本信息”选项卡中，输入或选择以下信息：

   > [!div class="mx-imgBorder"]
   > ![创建虚拟网络][1]

1. 单击“下一步:IP 地址 >”并输入或选择以下信息：

   > [!div class="mx-imgBorder"]
   >![配置 IP 地址][2]

1. 在“子网”部分，单击“+ 添加子网”并输入以下信息，然后单击“添加” 

   > [!div class="mx-imgBorder"]
   >![添加子网][3]

1. 单击“查看 + 创建”

1. 通过验证后，单击“创建”。

### <a name="create-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “计算” > “虚拟机”  

1. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

   > [!div class="mx-imgBorder"]
   >![虚拟机基本信息][4]

1. 选择“下一步:磁盘”

   保留默认设置。

1. 选择“下一步:网络”，然后选择以下信息：

   > [!div class="mx-imgBorder"]
   >![网络][5]

1. 单击“查看 + 创建”

1. 显示通过了验证的消息后，单击“创建”

## <a name="create-your-web-app-and-private-endpoint"></a>创建 Web 应用和专用终结点

在本部分，你将创建一个要通过专用终结点连接到的 Web 应用。

> [!Note]
>专用终结点功能仅适用于高级 V2 SKU。

### <a name="web-app"></a>Web 应用

1. 在 Azure 门户屏幕的左上方，选择 "**创建资源**" "  >  **web**  >  **应用**"

1. 在“创建 Web 应用 - 基本信息”中，输入或选择以下信息：

   > [!div class="mx-imgBorder"]
   >![Web 应用基本信息][6]

1. 选择“查看 + 创建”

1. 显示通过了验证的消息后，单击“创建”

### <a name="create-the-private-endpoint"></a>创建专用终结点

1. 在 Web 应用属性中，选择“设置” > “网络”，然后单击“配置专用终结点连接”  

   > [!div class="mx-imgBorder"]
   >![Web 应用网络][7]

1. 在向导中单击“+ 添加”

   > [!div class="mx-imgBorder"]
   >![Web 应用专用终结点][8]

1. 填写订阅、VNet 和子网信息，然后单击“确定”

   > [!div class="mx-imgBorder"]
   >![Web 应用网络][9]

1. 查看专用终结点的创建结果

   > [!div class="mx-imgBorder"]
   >![查看][10]
   >![专用终结点的最终视图][11]

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

1. 在门户的搜索栏中，输入 myVm。
1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开，请选择“RDP”

   > [!div class="mx-imgBorder"]
   >![“RDP”按钮][12]

1. 单击“下载 RDP”文件后，Azure 会创建远程桌面协议 (.rdp) 文件，并将其下载到计算机

   > [!div class="mx-imgBorder"]
   >![下载 RDP 文件][13]

1. 打开 downloaded.rdp 文件。

   - 出现提示时，选择“连接”。
   - 输入在创建 VM 时指定的用户名和密码。

   > [!Note]
   > 可能需要选择“更多选择”>“使用其他帐户”，以指定在创建 VM 时输入的凭据。

   - 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续” 。

1. VM 桌面出现后，将其最小化以返回到本地桌面。

## <a name="access-web-app-privately-from-the-vm"></a>从 VM 以私密方式访问 Web 应用

在本部分，你将使用专用终结点以私密方式连接到 Web 应用。

1. 获取专用终结点的专用 IP，在搜索栏中键入“专用链接”，然后选择“专用链接”

   > [!div class="mx-imgBorder"]
   >![专用链接][14]

1. 在专用链接中心，选择“专用终结点”以列出所有专用终结点

   > [!div class="mx-imgBorder"]
   >![专用链接中心][15]

1. 选择指向 Web 应用和子网的专用终结点链接

   > [!div class="mx-imgBorder"]
   >![专用终结点属性][16]

1. 复制专用终结点的专用 IP 和 Web 应用的 FQDN，在本例中为 webappdemope.azurewebsites.net 10.10.2。4

1. 在 myVM 中，验证是否无法通过公共 IP 访问 Web 应用。 打开浏览器并粘贴 Web 应用名称，此时必须出现“403 禁止”错误页

   > [!div class="mx-imgBorder"]
   >![尝试使用 IP 地址时出现错误禁止][17]

   > [!Important]
   > 由于此功能目前为预览版，因此需要手动管理 DNS 条目。

   对于 DNS，有两种选择：
   - 使用 VM 的主机文件 
   - 或使用 Azure DNS 专用区域服务。

1. 第一个解决方案：你可以创建名为 privatelink.azurewebsites.net 的 DNS 专用区，并将其链接到 VNet
1. 然后，需要使用专用终结点的 IP 地址创建两个 A 记录 (应用名称和 SCM 名称) 
   > [!div class="mx-imgBorder"]
   >![DNS 专用区域记录][21]

1. 第二个解决方案：创建主机项，打开文件资源管理器并找到 hosts 文件

   > [!div class="mx-imgBorder"]
   >![Hosts 文件][18]

1. 使用记事本编辑 hosts 文件，添加一个包含专用 IP 地址和 Web 应用公用名的条目

   > [!div class="mx-imgBorder"]
   >![Hosts 内容][19]

1. 保存文件

1. 打开浏览器并键入 Web 应用的 URL

   > [!div class="mx-imgBorder"]
   >![包含 PE 的网站][20]

1. 你正在通过专用终结点访问 Web 应用

## <a name="clean-up-resources"></a>清理资源

用完专用终结点、Web 应用和 VM 之后，请删除资源组及其包含的所有资源：

1. 在门户顶部的搜索框中输入 ready-rg，然后从搜索结果中选择 ready-rg。
1. 选择“删除资源组”。
1. 在“键入资源组名称”字段中输入 ready-rg，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你在虚拟网络上创建了一个 VM、一个 Web 应用和一个专用终结点。 你已从 Internet 连接到某个 VM，并使用专用链接安全地与 Web 应用进行通信。 若要了解有关专用终结点的详细信息，请参阅 [什么是 Azure 专用终结点][privateendpoint]。

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
