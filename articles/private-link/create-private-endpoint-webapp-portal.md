---
title: 使用 Azure 专用终结点将专用连接到 web 应用
description: 本文介绍如何使用 Azure 专用终结点将专用连接到 web 应用。
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 10/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3e0f05240aba9b5c92689315e409aaabe793b3f4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772832"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint"></a>使用 Azure 专用终结点将专用连接到 web 应用

Azure 专用终结点是 Azure 专用链接的基本构建基块。 通过使用专用终结点，你可以私下连接到你的 web 应用。 本文介绍如何使用专用终结点部署 web 应用，然后从虚拟机 (VM) 连接到 web 应用。

有关详细信息，请参阅 [将专用终结点用于 Azure web 应用][privateendpointwebapp]。

> [!Note]
> 专用终结点在公共区域中提供，用于 PremiumV2 层、PremiumV3 Windows web 应用、Linux web 应用，以及 Azure Functions 高级计划 (有时称为 "弹性高级计划) "。 

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在开始之前，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-virtual-network-and-virtual-machine"></a>创建虚拟网络和虚拟机

在本部分中，将创建一个虚拟网络和子网，以托管将用于通过专用终结点访问 web 应用的 VM。

### <a name="create-the-virtual-network"></a>创建虚拟网络

若要创建虚拟网络和子网，请执行以下操作：

1. 在左侧窗格中，选择 "**创建资源**" "网络" "  >  **Networking**  >  **虚拟网络**"。

1. 在 " **创建虚拟网络** " 窗格上，选择 " **基本** 信息" 选项卡，然后输入此处显示的信息：

   > [!div class="mx-imgBorder"]
   > ![Azure 门户中的 "创建虚拟网络" 窗格的屏幕截图。][1]

1. 选择 " **IP 地址** " 选项卡，然后输入此处显示的信息：

   > [!div class="mx-imgBorder"]
   > !["创建虚拟网络" 窗格上的 "IP 地址" 选项卡的屏幕截图。][2]

1. 在 " **子网** " 部分中，选择 " **添加子网**"，输入此处显示的信息，然后选择 " **添加**"。

   > [!div class="mx-imgBorder"]
   > !["添加子网" 窗格的屏幕截图。][3]

1. 选择“查看 + 创建”  。

1. 验证成功后，选择 " **创建**"。

### <a name="create-the-virtual-machine"></a>创建虚拟机

若要创建虚拟机，请执行以下操作：

1. 在 Azure 门户的左窗格中，选择 "**创建资源**" "计算" "  >  **Compute**  >  **虚拟机**"。

1. 在 " **创建虚拟机-基础知识** " 窗格上，输入此处显示的信息：

   > [!div class="mx-imgBorder"]
   > !["创建虚拟机" 窗格的屏幕截图。][4]

1. 在完成时选择“下一步:**磁盘”** 。

1. 在 " **磁盘** " 窗格中，保留默认设置，然后选择 " **下一步：网络**"。

1. 在 " **网络** " 窗格中，输入如下所示的信息：

   > [!div class="mx-imgBorder"]
   > !["创建虚拟机" 窗格上的 "网络" 选项卡的屏幕截图。][5]

1. 选择“查看 + 创建”  。

1. 验证成功后，选择 " **创建**"。

## <a name="create-a-web-app-and-a-private-endpoint"></a>创建 web 应用和专用终结点

在本部分中，将创建一个使用专用终结点的专用 web 应用。

> [!Note]
> 专用终结点功能仅适用于 PremiumV2 和 PremiumV3 层。

### <a name="create-the-web-app"></a>创建 Web 应用

1. 在 Azure 门户的左窗格中，选择 "**创建资源**" "  >  **web**  >  **web 应用**"。

1. 在 " **Web 应用** " 窗格上，选择 " **基本** 信息" 选项卡，然后输入此处显示的信息：

   > [!div class="mx-imgBorder"]
   > !["Web 应用" 窗格上的 "基本信息" 选项卡的屏幕截图。][6]

1. 选择“查看 + 创建”  。

1. 验证成功后，选择 " **创建**"。

### <a name="create-the-private-endpoint"></a>创建专用终结点

1. 在 "web 应用属性" 的 " **设置**" 下，选择 " **网络**"，然后在 "专用终结点连接" 下选择 " **配置专用终结点连接**"。

   > [!div class="mx-imgBorder"]
   > ![Web 应用网络窗格上的 "配置专用终结点连接" 链接的屏幕截图。][7]

1. 在 **专用终结点连接** 向导中，选择 " **添加**"。

   > [!div class="mx-imgBorder"]
   > !["专用终结点连接" 向导中的 "添加" 按钮的屏幕截图。][8]

1. 在 " **订阅**"、" **虚拟网络**" 和 " **子网** " 下拉列表中选择正确的信息，然后选择 **"确定"**。

   > [!div class="mx-imgBorder"]
   > !["添加专用终结点" 窗格的屏幕截图。][9]

1. 监视专用终结点的创建进度。

   > [!div class="mx-imgBorder"]
   > ![添加专用终结点的进度屏幕截图。 ][10]
   >  ![新创建的专用终结点的屏幕截图。][11]

## <a name="connect-to-the-vm-from-the-internet"></a>从 internet 连接到 VM

1. 在 Azure 门户 **搜索** 框中，输入 **myVm**。
1. 选择 " **连接**"，然后选择 " **RDP**"。

   > [!div class="mx-imgBorder"]
   > !["MyVM" 窗格上的 "RDP" 按钮的屏幕截图。][12]

1. 在 " **与 RDP 连接** " 窗格中，选择 " **下载 rdp 文件**"。  

   > [!div class="mx-imgBorder"]
   > !["通过 RDP 连接" 窗格上的 "下载 RDP 文件" 按钮的屏幕截图。][13]

   Azure 会创建一个远程桌面协议 (RDP) 文件，并将其下载到计算机。   

1. 打开下载的 RDP 文件。

   a. 在提示符下，选择 " **连接**"。  
   b. 输入创建 VM 时指定的用户名和密码。

     > [!Note]
     > 若要使用这些凭据，你可能需要选择 "**更多**选择" "  >  **使用其他帐户**"。

1. 选择“确定”****。

   > [!Note]
   > 如果在登录过程中收到证书警告，请选择 **"是"** 或 " **继续**"。

1. 当 VM 桌面窗口出现时，将其最小化以返回到本地桌面。

## <a name="access-the-web-app-privately-from-the-vm"></a>从 VM 私下访问 web 应用

在本部分中，将使用专用终结点将专用连接到 web 应用。

1. 若要获取专用终结点的专用 IP，请在 **搜索** 框中键入 " **专用链接** "，然后在结果列表中选择 " **专用链接**"。

   > [!div class="mx-imgBorder"]
   > ![搜索结果列表中的 "专用链接" 链接的屏幕截图。][14]

1. 在 "专用链接中心" 的左窗格中，选择 " **专用终结点** " 以显示专用终结点。

   > [!div class="mx-imgBorder"]
   > ![专用链接中心中的专用终结点列表的屏幕截图。][15]

1. 选择链接到你的 web 应用和子网的专用终结点。

   > [!div class="mx-imgBorder"]
   > ![专用终结点的 "属性" 窗格的屏幕截图。][16]

1. 将专用终结点的专用 IP 和完全限定的域名复制 (web 应用的 FQDN) 。 在前面的示例中，专用 ID 是 *`webappdemope.azurewebsites.net 10.10.2.4`* 。

1. 在 " **myVM** " 窗格上，验证 web 应用是否无法通过公共 IP 访问。 为此，请打开浏览器并粘贴 web 应用名称。 该页应显示 "错误 403-禁止显示" 消息。

   > [!div class="mx-imgBorder"]
   > !["错误 403-禁止访问" 错误页的屏幕截图。][17]

   对于 DNS，执行以下任一操作：
 
   - 使用 Azure DNS 专用区域服务。  

     a. 创建名为的 DNS 专用区 *`privatelink.azurewebsites.net`* ，并将其链接到虚拟网络。  
     b. 创建两个 A 记录 (即，应用名称和服务控制管理器 [SCM] 名称) 使用专用终结点的 IP 地址。  
     > [!div class="mx-imgBorder"]
     > ![DNS 专用区域记录的屏幕截图。][21]  

   - 使用 VM 的 *hosts* 文件。  

     a. 创建 hosts 条目，打开文件资源管理器，然后查找 *hosts* 文件。  
     > [!div class="mx-imgBorder"]
     > ![显示文件资源管理器中的主机文件的屏幕截图。][18]  
     b. 通过在文本编辑器中编辑 *hosts* 文件，添加包含 web 应用的专用 IP 地址和公共名称的条目。  
     > [!div class="mx-imgBorder"]
     > ![Hosts 文件的文本屏幕截图。][19]  
     c. 保存文件。

1. 在浏览器中，键入 web 应用的 URL。

   > [!div class="mx-imgBorder"]
   > ![显示 web 应用的浏览器的屏幕截图。][20]

你现在将通过专用终结点访问你的 web 应用。

## <a name="clean-up-resources"></a>清理资源

使用完专用终结点、web 应用和 VM 后，请删除资源组及其包含的所有资源。

1. 在 "Azure 门户的" **搜索** "框中，输入 **ready-rg**，然后在结果列表中选择" **ready-rg** "。

1. 选择“删除资源组”****。

1. 在 " **键入资源组名称**" 下，输入 " **就绪-rg**"，然后选择 " **删除**"。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在虚拟网络、web 应用和专用终结点上创建 VM。 你已从 internet 连接到 VM，并使用专用链接安全地传达到 web 应用。 

若要详细了解专用终结点，请参阅[什么是 Azure 专用终结点？][privateendpoint]。

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
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
