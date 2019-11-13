---
title: 配置子网流量路由-Azure 流量管理器
description: 本文介绍了如何配置流量管理器以从特定子网路由流量。
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: allensu
ms.openlocfilehash: d3751a14e8c317d6a4f23c1aa051b7e13305acf5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014612"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>使用流量管理器，基于用户子网，将流量定向到特定终结点

本文介绍如何配置子网流量路由方法。 借助子网流量路由方法，可以将一组 IP 地址范围映射到特定终结点，当流量管理器接收到请求后，它会检查请求的源 IP 并返回相关的终结点。

在本文讨论的方案中，使用子网路由时，流量将路由到内部网站或生产网站，具体取决于用户查询的 IP 地址。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
为了查看流量管理器的运作方式，本教程要求部署以下各项：
- 两个基本网站在不同的 Azure 区域运行 - 美国东部（作为内部网站）和西欧（作为生产网站）。
- 用于测试流量管理器的两个测试 VM - 一个 VM 位于“美国东部”，另一个 VM 位于“西欧”。

测试 VM 用于演示流量管理器如何基于用户查询发起的子网，将用户流量路由到内部网站或生产网站。

### <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

### <a name="create-websites"></a>创建网站

在本部分，我们将创建两个网站实例，以便为 Azure 区域中的流量管理器配置文件提供两个服务终结点。 通过以下步骤创建两个网站：
1. 创建两个用于运行基本网站的 VM - 一个 VM 位于“美国东部”，另一个位于“西欧”。
2. 在每个 VM 上安装 IIS 服务器，并更新默认网站页面，该页面描述当用户访问网站时要连接到的 VM 名称。

#### <a name="create-vms-for-running-websites"></a>创建用于运行网站的 VM
在本部分，我们将在“美国东部”和“西欧”Azure 区域创建两个 VM：myEndpointVMEastUS 和 myEndpointVMWEurope。

1. 在 Azure 门户的左上角选择“创建资源” **“计算”** “Windows Server 2016 VM”。 >  > 
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
    |虚拟网络| 选择“虚拟网络”，在“创建虚拟网络”中，为“名称”输入 **myVNet1**，为“子网”输入 **mySubnet**。|
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
    |虚拟网络 | 选择“虚拟网络”，在“创建虚拟网络”中，为“名称”输入 **myVNet2**，为“子网”输入 **mySubnet**。|
    |||

8. 创建 VM 可能需要数分钟的时间。 在两个 VM 完成创建之前，不要继续执行剩余的步骤。

   ![创建 VM](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>安装 IIS 并自定义默认网页

在本部分，我们将在 *myIISVMEastUS*  &  和 *myIISVMWEurope* 这两个 VM 上安装 IIS 服务器，然后更新默认网站页面。 自定义网站页面显示从 Web 浏览器访问网站时要连接到的 VM 的名称。

1. 在左侧菜单中选择“所有资源”，然后在资源列表中，单击位于 **myResourceGroupTM1** 资源组中的“myIISVMEastUS”。
2. 在“概述”页上单击“连接”，然后在“连接到虚拟机”中选择“下载 RDP 文件”。
3. 打开下载的 rdp 文件。 出现提示时，选择“连接”。 输入在创建 VM 时指定的用户名和密码。 可能需要选择“更多选择”，然后选择“使用其他帐户”，以指定在创建 VM 时输入的凭据。
4. 选择“确定”。
5. 你可能会在登录过程中收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接。
6. 在服务器桌面上导航到“Windows 管理工具”>“服务器管理器”。
7. 在 myIISVMEastUS 上启动 Windows PowerShell 并使用以下命令安装 IIS 服务器并更新默认的 htm 文件。
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. 关闭与 *myIISVMEastUS* 之间的 RDP 连接。
9. 重复步骤 1-6，与 myResourceGroupTM2 资源组中的 VM myIISVMWEurope 建立 RDP 连接，以安装 IIS 并自定义其默认网页。
10. 在 myIISVMWEurope 上启动 Windows PowerShell，使用以下命令安装 IIS 服务器并更新默认的 htm 文件。
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>为运行 IIS 的 VM 配置 DNS 名称

流量管理器基于服务终结点的 DNS 名称路由用户流量。 在此部分中，将为 IIS 服务器配置 DNS 名称 - myIISVMEastUS和 myIISVMWEurope。

1. 在左侧菜单中单击“所有资源”，然后在资源列表中，选择位于 **myResourceGroupTM1** 资源组中的“myIISVMEastUS”。
2. 在“概述”页上的“DNS 名称”下，选择“配置”。
3. 在“配置”页上的 DNS 名称标签下添加唯一的名称，然后选择“保存”。
4. 针对 *myResourceGroupTM1* 资源组中名为 *myIISVMWEurope* 的 VM 重复步骤 1-3。

### <a name="create-test-vms"></a>创建测试 VM

在本部分，我们将在每个 Azure 区域（“美国东部”和“西欧”）各创建一个 VM（*mVMEastUS* 和 *myVMWestEurope*）。 稍后将使用这些 VM 来测试当你浏览到该网站时，流量管理器如何将流量路由到最近的 IIS 服务器。

1. 在 Azure 门户的左上角选择“创建资源” **“计算”** “Windows Server 2016 VM”。 >  > 
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
    |虚拟网络| 选择“虚拟网络”，在“创建虚拟网络”中，为“名称”输入 myVNet3，为“子网”输入 mySubnet3。|
    |网络安全组|选择“基本”，在“选择公共入站端口”下拉列表中选择“HTTP”和“RDP” |
    |启动诊断|选择“已禁用”。|
    |||

6. 在“摘要”中的“创建”下，选择“创建”以启动 VM 部署。

7. 再次完成步骤 1-5，但需要做出以下更改：

    |设置|值|
    |---|---|
    |VM 名称 | *myVMWEurope*|
    |资源组 | 选择“现有”，然后键入 **myResourceGroupTM2**|
    |虚拟网络 | 选择“虚拟网络”，在“创建虚拟网络”中，为“名称”输入 myVNet4，为“子网”输入 mySubnet4。|
    |||

8. 创建 VM 可能需要数分钟的时间。 在两个 VM 完成创建之前，不要继续执行剩余的步骤。

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件
创建一个流量管理器配置文件，该配置文件允许根据请求的源 IP 返回特定终结点。

1. 在屏幕左上方，选择“创建资源” > “网络” > “流量管理器配置文件” > “创建”。
2. 在“创建流量管理器配置文件”中输入或选择以下信息，接受剩下的默认设置，然后选择“创建”：

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 名称                   | 此名称必须在 trafficmanager.net 区域中唯一，并可生成用于访问流量管理器配置文件的 DNS 名称 (trafficmanager.net)。                                   |
    | 路由方法          | 选择“子网”路由方法。                                       |
    | 订阅            | 选择订阅。                          |
    | 资源组          | 选择“现有”，然后输入 myResourceGroupTM1。 |
    | |                              |
    |

    ![创建流量管理器配置文件](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

添加运行 IIS 服务器的两个 VM (myIISVMEastUS*myIISVMWEurope)，根据用户查询的子网路由用户流量* & 。

1. 在门户的搜索栏中，搜索在前面部分创建的流量管理器配置文件名称，并在显示的结果中选择该配置文件。
2. 在“流量管理器配置文件”的“设置”部分单击“终结点”，然后单击“添加”。
3. 输入或选择以下信息，保留剩下的默认设置，然后选择“确定”：

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 类型                    | Azure 终结点                                   |
    | 名称           | myTestWebSiteEndpoint                                        |
    | 目标资源类型           | 公共 IP 地址                          |
    | 目标资源          | **选择公共 IP 地址**以显示同一订阅下具有公共 IP 地址的资源列表。 在“资源”中，选择名为 **myIISVMEastUS-ip** 的公共 IP 地址。 这是美国东部的 IIS 服务器 VM 的公共 IP 地址。|
    |  子网路由设置    |   添加 myVMEastUS 测试 VM 的 IP 地址。 源自此 VM 的任何用户查询都将定向到 myTestWebSiteEndpoint。    |

4. 重复步骤 2 和 3，为 IIS 服务器 VM myIISVMWEurope 关联的公共 IP 地址 myIISVMWEurope-ip 添加名为 myProductionEndpoint 的另一个终结点。 对于“子网路由”设置，添加测试 VM myVMWestEurope 的 IP 地址。 源自此测试 VM 的任何用户查询都将路由到终结点 myProductionWebsiteEndpoint。
5. 添加完这两个终结点后，这两个终结点会显示在“流量管理器配置文件”中，并且其监视状态为“联机”。

    ![添加流量管理器终结点](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)

## <a name="test-traffic-manager-profile"></a>测试流量管理器配置文件
本部分测试流量管理器如何将用户流量从给定子网路由到特定终结点。 若要查看流量管理器的运作方式，请完成以下步骤：
1. 确定流量管理器配置文件的 DNS 名称。
2. 按如下所述查看流量管理器的运作方式：
    - 在位于“美国东部”区域的测试 VM (*myVMEastUS*) 上，通过 Web 浏览器浏览到流量管理器配置文件的 DNS 名称。
    - 在位于“西欧”区域的测试 VM (*myVMEastUS*) 上，通过 Web 浏览器浏览到流量管理器配置文件的 DNS 名称。

### <a name="determine-dns-name-of-traffic-manager-profile"></a>确定流量管理器配置文件的 DNS 名称
在本教程中，为简单起见，我们将使用流量管理器配置文件的 DNS 名称来访问网站。

可按如下所述确定流量管理器配置文件的 DNS 名称：

1. 在门户的搜索栏中，搜索在前面部分中创建的**流量管理器配置文件**名称。 在显示的结果中，单击流量管理器配置文件。
1. 单击“概览”。
2. “流量管理器配置文件”会显示新建的流量管理器配置文件的 DNS 名称。 在生产部署中，可以使用 DNS CNAME 记录配置一个指向流量管理器域名的虚构域名。

   ![流量管理器 DNS 名称](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>查看正在运行的流量管理器
在本部分，我们可以查看流量管理器的运作方式。

1. 在左侧菜单中选择“所有资源”，然后在资源列表中，单击位于 **myResourceGroupTM1** 资源组中的“myVMEastUS”。
2. 在“概述”页上单击“连接”，然后在“连接到虚拟机”中选择“下载 RDP 文件”。
3. 打开下载的 rdp 文件。 出现提示时，选择“连接”。 输入在创建 VM 时指定的用户名和密码。 可能需要选择“更多选择”，然后选择“使用其他帐户”，以指定在创建 VM 时输入的凭据。
4. 选择“确定”。
5. 你可能会在登录过程中收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接。
1. 在 VM *myVMEastUS* 上的 Web 浏览器中，键入流量管理器配置文件的 DNS 名称，以查看网站。 由于 VM myVMEastUS IP 地址与终结点 myIISVMEastUS 关联，因此 Web 浏览器将启动测试网站服务器 - myIISVMEastUS。

   ![测试流量管理器配置文件](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. 接下来，使用步骤 1-5 连接到位于“西欧”的 VM *myVMWestEurope*，然后从此 VM 浏览到流量管理器配置文件域名。 由于 VM myVMWestEurope IP 地址与终结点 myIISVMEastUS 关联，因此 Web 浏览器将启动测试网站服务器 - myIISVMWEurope。

## <a name="delete-the-traffic-manager-profile"></a>删除流量管理器配置文件
不再需要时，请删除资源组（**ResourceGroupTM1** 和 **ResourceGroupTM2**）。 为此，请选择资源组（**ResourceGroupTM1** 或 **ResourceGroupTM2**），然后选择“删除”。

## <a name="next-steps"></a>后续步骤

- 了解[加权流量路由方法](traffic-manager-configure-weighted-routing-method.md)。
- 了解[优先级路由方法](traffic-manager-configure-priority-routing-method.md)。
- 了解[地理路由方法](traffic-manager-configure-geographic-routing-method.md)。
