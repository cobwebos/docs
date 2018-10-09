---
title: 教程 - 使用 Azure 流量管理器路由流量以改善网站响应 | Microsoft Docs
description: 本教程文章介绍如何创建流量管理器配置文件，以生成具有高响应能力的网站。
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: jeconnoc
editor: ''
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: kumud
ms.openlocfilehash: 087dcda5826d96ad064c472fc897be7e61133387
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392495"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>教程：使用流量管理器改善网站响应 

本教程介绍如何使用流量管理器创建具有高响应能力的网站，并以较低的延迟将用户流量定向到该网站。 通常，地理距离最短的数据中心的延迟最低。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 IIS 上创建两个运行基本网站的 VM
> * 创建两个测试 VM 以查看流量管理器的运作方式
> * 为运行 IIS 的 VM 配置 DNS 名称
> * 创建流量管理器配置文件以改善网站性能
> * 将 VM 终结点添加到流量管理器配置文件
> * 查看正在运行的流量管理器

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
为了查看流量管理器的运作方式，本教程要求部署以下各项：
- 在不同 Azure 区域（“美国东部”和“西欧”）运行的基本网站的两个实例。
- 用于测试流量管理器的两个测试 VM - 一个 VM 位于“美国东部”，另一个 VM 位于“西欧”。 测试 VM 用于演示流量管理器如何将用户流量路由到同一区域中运行的网站，并提供最低的延迟。

### <a name="sign-in-to-azure"></a>登录 Azure 

通过 https://portal.azure.com 登录到 Azure 门户。

### <a name="create-websites"></a>创建网站

在本部分，我们将创建两个网站实例，以便为 Azure 区域中的流量管理器配置文件提供两个服务终结点。 通过以下步骤创建两个网站：
1. 创建两个用于运行基本网站的 VM - 一个 VM 位于“美国东部”，另一个位于“西欧”。
2. 在每个 VM 上安装 IIS 服务器，并更新默认网站页面，该页面描述当用户访问网站时要连接到的 VM 名称。

#### <a name="create-vms-for-running-websites"></a>创建用于运行网站的 VM
在本部分，我们将在“美国东部”和“西欧”Azure 区域创建两个 VM：*myIISVMEastUS* 和 *myIISVMWEurope*。

1. 在 Azure 门户的左上角选择“创建资源” > “计算” > “Windows Server 2016 VM”。
2. 对于“基本信息”输入或选择以下信息，接受剩下的默认设置，然后选择“创建”：

    |设置|值|
    |---|---|
    |名称|myIISVMEastUS|
    |用户名| 输入所选用户名。|
    |密码| 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    |资源组| 选择“新建”，然后键入 *myResourceGroupTM1*。|
    |位置| 选择“美国东部”。|
    |||
4. 在“选择大小”下选择 VM 大小。
5. 对于“设置”选择以下值，然后选择“确定”：
    
    |设置|值|
    |---|---|
    |虚拟网络| 选择“虚拟网络”，在“创建虚拟网络”中，为“名称”输入 *myVNet1*，为“子网”输入 *mySubnet*。|
    |网络安全组|选择“基本”，在“选择公共入站端口”下拉列表中选择“HTTP”和“RDP” |
    |启动诊断|选择“已禁用”。|
    |||
6. 在“摘要”中的“创建”下，选择“创建”以启动 VM 部署。

7. 再次完成步骤 1-6，并做出以下更改：

    |设置|值|
    |---|---|
    |资源组 | 选择“新建”，然后键入 *myResourceGroupTM2*|
    |位置|西欧|
    |VM 名称 | myIISVMWEurope|
    |虚拟网络 | 选择“虚拟网络”，在“创建虚拟网络”中，为“名称”输入 *myVNet2*，为“子网”输入 *mySubnet*。|
    |||
8. 创建 VM 可能需要数分钟的时间。 在两个 VM 完成创建之前，不要继续执行剩余的步骤。

   ![创建 VM](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>安装 IIS 并自定义默认网页

在本部分，我们将在 *myIISVMEastUS* 和 *myIISVMWEurope* 这两个 VM 上安装 IIS 服务器，然后更新默认网站页面。 自定义网站页面显示从 Web 浏览器访问网站时要连接到的 VM 的名称。

1. 在左侧菜单中选择“所有资源”，然后在资源列表中，单击位于 *myResourceGroupTM1* 资源组中的“myIISVMEastUS”。
2. 在“概述”页上单击“连接”，然后在“连接到虚拟机”中选择“下载 RDP 文件”。 
3. 打开下载的 rdp 文件。 出现提示时，选择“连接”。 输入在创建 VM 时指定的用户名和密码。 可能需要选择“更多选择”，然后选择“使用其他帐户”，以指定在创建 VM 时输入的凭据。 
4. 选择“确定”。
5. 你可能会在登录过程中收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接。
6. 在服务器桌面上导航到“Windows 管理工具”>“服务器管理器”。
7. 在 VM1 上启动 Windows PowerShell 并使用以下命令安装 IIS 服务器并更新默认的 htm 文件。
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![安装 IIS 并自定义网页](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. 关闭与 *myIISVMEastUS* 之间的 RDP 连接。
9. 重复步骤 1-8，与 *myResourceGroupTM2* 资源组中的 VM *myIISVMWEurope* 建立 RDP 连接，以安装 IIS 并自定义其默认网页。

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>为运行 IIS 的 VM 配置 DNS 名称

流量管理器基于服务终结点的 DNS 名称路由用户流量。 在本部分，我们将为 IIS 服务器 *myIISVMEastUS* 和 *myIISVMWEurope* 配置 DNS 名称。

1. 在左侧菜单中单击“所有资源”，然后在资源列表中，选择位于 *myResourceGroupTM1* 资源组中的“myIISVMEastUS”。
2. 在“概述”页上的“DNS 名称”下，选择“配置”。
3. 在“配置”页上的 DNS 名称标签下添加唯一的名称，然后选择“保存”。
4. 针对 *myResourceGroupTM1* 资源组中名为 *myIISVMWEurope* 的 VM 重复步骤 1-3。

### <a name="create-test-vms"></a>创建测试 VM

在本部分，我们将在每个 Azure 区域（“美国东部”和“西欧”）各创建一个 VM（*mVMEastUS* 和 *myVMWestEurope*）。 稍后将使用这些 VM 来测试当你浏览到该网站时，流量管理器如何将流量路由到最近的 IIS 服务器。

1. 在 Azure 门户的左上角选择“创建资源” > “计算” > “Windows Server 2016 VM”。
2. 对于“基本信息”输入或选择以下信息，接受剩下的默认设置，然后选择“创建”：

    |设置|值|
    |---|---|
    |名称|myVMEastUS|
    |用户名| 输入所选用户名。|
    |密码| 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    |资源组| 选择“现有”，然后选择“myResourceGroupTM1”。|
    |||

4. 在“选择大小”下选择 VM 大小。
5. 对于“设置”选择以下值，然后选择“确定”：
    |设置|值|
    |---|---|
    |虚拟网络| 选择“虚拟网络”，在“创建虚拟网络”中，为“名称”输入 *myVNet3*，为“子网”输入 *mySubnet*。|
    |网络安全组|选择“基本”，在“选择公共入站端口”下拉列表中选择“HTTP”和“RDP” |
    |启动诊断|选择“已禁用”。|
    |||

6. 在“摘要”中的“创建”下，选择“创建”以启动 VM 部署。

7. 再次完成步骤 1-5，但需要做出以下更改：

    |设置|值|
    |---|---|
    |VM 名称 | *myVMWEurope*|
    |资源组 | 选择“现有”，然后键入 *myResourceGroupTM2*|
    |虚拟网络 | 选择“虚拟网络”，在“创建虚拟网络”中，为“名称”输入 *myVNet4*，为“子网”输入 *mySubnet*。|
    |||

8. 创建 VM 可能需要数分钟的时间。 在两个 VM 完成创建之前，不要继续执行剩余的步骤。

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件
创建流量管理器配置文件，以便将用户流量定向到延迟最低的终结点。

1. 在屏幕左上方，选择“创建资源” > “网络” > “流量管理器配置文件” > “创建”。
2. 在“创建流量管理器配置文件”中输入或选择以下信息，接受剩下的默认设置，然后选择“创建”：
    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 名称                   | 此名称必须在 trafficmanager.net 区域中唯一，并可生成用于访问流量管理器配置文件的 DNS 名称 (trafficmanager.net)。                                   |
    | 路由方法          | 选择“性能”路由方法。                                       |
    | 订阅            | 选择订阅。                          |
    | 资源组          | 选择“新建”，然后输入 *myResourceGroupTM1*。 |
    | 位置                | 选择“美国东部”。  此设置指的是资源组的位置，对将全局部署的流量管理器配置文件没有影响。                              |
    |
  
    ![创建流量管理器配置文件](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

添加两个运行 IIS 服务器的 VM（*myIISVMEastUS* 和 *myIISVMWEurope*），以便将用户流量路由到最靠近用户的终结点。

1. 在门户的搜索栏中，搜索在前面部分创建的流量管理器配置文件名称，并在显示的结果中选择该配置文件。
2. 在“流量管理器配置文件”的“设置”部分单击“终结点”，然后单击“添加”。
3. 输入或选择以下信息，保留剩下的默认设置，然后选择“确定”：

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | Type                    | Azure 终结点                                   |
    | 名称           | myEastUSEndpoint                                        |
    | 目标资源类型           | 公共 IP 地址                          |
    | 目标资源          | **选择公共 IP 地址**以显示同一订阅下具有公共 IP 地址的资源列表。 在“资源”中，选择名为 *myIISVMEastUS-ip* 的公共 IP 地址。 这是美国东部的 IIS 服务器 VM 的公共 IP 地址。|
    |        |           |

4. 重复步骤 2 和 3，为 IIS 服务器 VM *myIISVMWEurope* 关联的公共 IP 地址 *myIISVMWEurope-ip* 添加名为 *myWestEuropeEndpoint* 的另一个终结点。
5.  添加完这两个终结点后，这两个终结点会显示在“流量管理器配置文件”中，并且其监视状态为“联机”。

    ![添加流量管理器终结点](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)
  

## <a name="test-traffic-manager-profile"></a>测试流量管理器配置文件
在本部分，我们将测试流量管理器如何将用户流量路由到运行网站的最近 VM，以提供最低的延迟。 若要查看流量管理器的运作方式，请完成以下步骤：
1. 确定流量管理器配置文件的 DNS 名称。
2. 按如下所述查看流量管理器的运作方式：
    - 在位于“美国东部”区域的测试 VM (*myVMEastUS*) 上，通过 Web 浏览器浏览到流量管理器配置文件的 DNS 名称。
    - 在位于“西欧”区域的测试 VM (*myVMEastUS*) 上，通过 Web 浏览器浏览到流量管理器配置文件的 DNS 名称。

### <a name="determine-dns-name-of-traffic-manager-profile"></a>确定流量管理器配置文件的 DNS 名称
在本教程中，为简单起见，我们将使用流量管理器配置文件的 DNS 名称来访问网站。 

可按如下所述确定流量管理器配置文件的 DNS 名称：

1.  在门户的搜索栏中，搜索在前面部分中创建的**流量管理器配置文件**名称。 在显示的结果中，单击流量管理器配置文件。
1. 单击“概览”。
2. “流量管理器配置文件”会显示新建的流量管理器配置文件的 DNS 名称。 在生产部署中，可以使用 DNS CNAME 记录配置一个指向流量管理器域名的虚构域名。

   ![流量管理器 DNS 名称](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>查看正在运行的流量管理器
在本部分，我们可以查看流量管理器的运作方式。 

1. 在左侧菜单中选择“所有资源”，然后在资源列表中，单击位于 *myResourceGroupTM1* 资源组中的“myVMEastUS”。
2. 在“概述”页上单击“连接”，然后在“连接到虚拟机”中选择“下载 RDP 文件”。 
3. 打开下载的 rdp 文件。 出现提示时，选择“连接”。 输入在创建 VM 时指定的用户名和密码。 可能需要选择“更多选择”，然后选择“使用其他帐户”，以指定在创建 VM 时输入的凭据。 
4. 选择“确定”。
5. 你可能会在登录过程中收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接。 
1. 在 VM *myVMEastUS* 上的 Web 浏览器中，键入流量管理器配置文件的 DNS 名称，以查看网站。 由于 VM 位于“美国东部”，因此你将路由到位于“美国东部”的最近 IIS 服务器 *myIISVMEastUS* 上托管的最近网站。

   ![测试流量管理器配置文件](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. 接下来，使用步骤 1-5 连接到位于“西欧”的 VM *myVMWestEurope*，然后从此 VM 浏览到流量管理器配置文件域名。  由于 VM 位于“西欧”，因此，现在你会路由到位于“西欧”的最近 IIS 服务器 *myIISVMWEurope* 上托管的网站。 

   ![测试流量管理器配置文件](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>删除流量管理器配置文件
不再需要时，请删除资源组（**ResourceGroupTM1** 和 **ResourceGroupTM2**）。 为此，请选择资源组（**ResourceGroupTM1** 或 **ResourceGroupTM2**），然后选择“删除”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将流量分配到一组终结点](traffic-manager-configure-weighted-routing-method.md)


