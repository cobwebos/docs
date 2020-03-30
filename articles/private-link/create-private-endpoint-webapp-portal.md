---
title: 使用 Azure 专用终结点私下连接到 Web 应用
description: 使用 Azure 专用终结点私下连接到 Web 应用
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287809"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>使用 Azure 专用终结点（预览）私下连接到 Web 应用

Azure 专用终结点是 Azure 中专用链接的构建基块。 它允许您私下连接到 Web 应用。
在此快速入门中，您将学习如何部署具有专用终结点的 Web 应用，以及如何从虚拟机连接到此 Web 应用。

有关详细信息，请参阅为[Azure Web 应用使用专用终结点][privatenedpointwebapp]。

> [!Note]
>预览版在美国东部和美国西部 2 区提供，适用于所有高级 V2 Windows 和 Linux Web 应用。 

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="virtual-network-and-virtual-machine"></a>虚拟网络和虚拟机

在本节中，您将创建虚拟网络和子网来承载用于通过专用终结点访问 Web 应用的 VM。

### <a name="create-the-virtual-network"></a>创建虚拟网络

在本节中，您将创建一个虚拟网络和子网。

1. 在屏幕的左上角，在搜索框中选择 **"创建资源** > **网络** > **虚拟网络**"或"搜索**虚拟网络**"。

1. 在 **"创建虚拟网络"** 中，在"基本"选项卡中输入或选择此信息：

   > [!div class="mx-imgBorder"]
   > ![创建虚拟网络][1]

1. 单击 **"下一步：IP 地址>"** 并输入或选择此信息：

   > [!div class="mx-imgBorder"]
   >![配置 IP 地址][2]

1. 在子网部分中，单击 **"+ 添加子网"** 并输入以下信息，然后单击 **"添加"**

   > [!div class="mx-imgBorder"]
   >![添加子网][3]

1. 单击 **"查看 + 创建"**

1. 验证通过后，单击 **"创建"**

### <a name="create-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户中屏幕的左上角，选择 **"创建资源** > **计算** > **虚拟机"**

1. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

   > [!div class="mx-imgBorder"]
   >![虚拟机基本][4]

1. 选择 **"下一步：磁盘"**

   保留默认设置。

1. 选择 **"下一步：网络"，** 选择此信息：

   > [!div class="mx-imgBorder"]
   >![网络][5]

1. 单击 **"查看 + 创建"**

1. 验证传递消息时，单击 **"创建"**

## <a name="create-your-web-app-and-private-endpoint"></a>创建 Web 应用和专用终结点

在本节中，您将使用专用终结点创建专用 Web 应用。

> [!Note]
>专用终结点功能仅适用于高级 V2 SKU。

### <a name="web-app"></a>Web 应用

1. 在 Azure 门户中屏幕的左上角，选择 **"创建资源** > **Web** > **Web 应用**"

1. 在"创建 Web 应用 - 基础知识"中，输入或选择此信息：

   > [!div class="mx-imgBorder"]
   >![Web 应用程序基本][6]

1. 选择 **"查看 + 创建"**

1. 验证传递消息时，单击 **"创建"**

### <a name="create-the-private-endpoint"></a>创建专用终结点

1. 在 Web 应用属性中，选择 **"设置** > **网络"，** 然后单击 **"配置专用终结点连接"**

   > [!div class="mx-imgBorder"]
   >![网络应用网络][7]

1. 在向导中，单击 **"+ 添加"**

   > [!div class="mx-imgBorder"]
   >![Web 应用专用终结点][8]

1. 填写订阅、VNet 和子网信息，然后单击 **"确定"**

   > [!div class="mx-imgBorder"]
   >![网络应用网络][9]

1. 查看私有终结点的创建

   > [!div class="mx-imgBorder"]
   >![查看][10]
   >![专用终结点的最终视图][11]

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

1. 在门户的搜索栏中，输入**myVm**
1. 选择"**连接"按钮**。 选择"连接"按钮后，打开"连接到虚拟机"，选择**RDP**

   > [!div class="mx-imgBorder"]
   >![RDP 按钮][12]

1. Azure 创建远程桌面协议 （.rdp） 文件，并在单击 **"下载 RDP 文件**"后将其下载到计算机

   > [!div class="mx-imgBorder"]
   >![下载 RDP 文件][13]

1. 打开 downloaded.rdp 文件。

- 出现提示时，选择“连接”。
- 输入在创建 VM 时指定的用户名和密码。

> [!Note]
> 可能需要选择“更多选择”>“使用其他帐户”，以指定在创建 VM 时输入的凭据。

- 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续”。

1. VM 桌面出现后，将其最小化以返回到本地桌面。

## <a name="access-web-app-privately-from-the-vm"></a>从 VM 私下访问 Web 应用

在本节中，您将使用专用终结点私下连接到 Web 应用。

1. 获取专用终结点的专用 IP，在搜索栏中键入**专用链接**，然后选择专用链接

   > [!div class="mx-imgBorder"]
   >![专用链接][14]

1. 在专用链接中心中，选择 **"专用终结点"** 以列出所有专用终结点

   > [!div class="mx-imgBorder"]
   >![专用链接中心][15]

1. 选择指向 Web 应用和子网的专用终结点链接

   > [!div class="mx-imgBorder"]
   >![专用终结点属性][16]

1. 复制您的专用终结点的专用 IP 和 Web 应用的 FQDN，在我们webappdemope.azurewebsites.net 10.10.2.4

1. 在 myVM 中，验证 Web 应用是否无法通过公共 IP 访问。 打开浏览器并粘贴 Web 应用名称，您必须有一个 403 禁止错误页面

   > [!div class="mx-imgBorder"]
   >![禁止][17]

> [!Important]
> 由于此功能处于预览状态，因此您需要手动管理 DNS 条目。

1. 创建主机条目，打开文件资源管理器并找到主机文件

   > [!div class="mx-imgBorder"]
   >![主机文件][18]

1. 使用记事本编辑主机文件，添加带有专用 IP 地址和 Web 应用公共名称的条目

   > [!div class="mx-imgBorder"]
   >![主机内容][19]

1. 保存

1. 打开浏览器并键入 Web 应用的 URL

   > [!div class="mx-imgBorder"]
   >![带 PE 的网站][20]

1. 您正在通过专用终结点访问 Web 应用

## <a name="clean-up-resources"></a>清理资源

使用专用终结点、Web 应用和 VM 完成后，请删除资源组及其包含的所有资源：

1. 在门户顶部的"搜索"框中输入就绪-rg，并从搜索结果中选择就绪 rg。
1. 选择“删除资源组”。
1. 输入"键入资源组名称"的就绪点，然后选择"删除"。

## <a name="next-steps"></a>后续步骤

在此快速入门中，您可以在虚拟网络、Web 应用和专用终结点上创建了 VM。 您从 Internet 连接到 VM，并使用专用链接安全地连接到 Web 应用。 要了解有关私有终结点的更多内容，请参阅[什么是 Azure 专用终结点][privateendpoint]。

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
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

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
