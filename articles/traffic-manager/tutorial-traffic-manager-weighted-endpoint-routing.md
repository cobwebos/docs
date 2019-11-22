---
title: 教程：将流量路由到加权终结点 - Azure 流量管理器
description: 本教程文章介绍如何使用流量管理器将流量路由到加权终结点。
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: allensu
ms.openlocfilehash: 45ece08599722e04c4e6799fa5c3589cba1fca42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037921"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>教程：使用流量管理器控制加权终结点的流量路由

本教程介绍如何通过 Azure 流量管理器使用加权路由方法控制用户流量在终结点之间的路由。 在此路由方法中，需要为流量管理器配置文件配置中的每个终结点分配权重。 然后，系统会根据分配给每个终结点的权重进行用户流量路由。 该权重是从 1 到 1,000 的整数。 分配给终结点的权重值越高，其优先级就越高。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 IIS 上创建两个运行基本网站的 VM。
> * 创建两个测试 VM 以查看流量管理器的运作方式。
> * 为运行 IIS 的 VM 配置 DNS 名称。
> * 创建流量管理器配置文件。
> * 将 VM 终结点添加到流量管理器配置文件。
> * 查看正在运行的流量管理器。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

为了查看流量管理器的运作方式，请为本教程部署以下各项：

- 在以下不同 Azure 区域运行的基本网站的两个实例：“美国东部”和“西欧”。
- 用于测试流量管理器的两个测试 VM：一个位于“美国东部”，另一个 VM 位于“西欧”。 测试 VM 用于演示流量管理器如何将用户流量路由到分配给其终结点的权重更高的网站。

### <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

### <a name="create-websites"></a>创建网站

在本部分，我们将创建两个网站实例，以便为 Azure 区域中的流量管理器配置文件提供两个服务终结点。 若要创建这两个网站，请完成以下步骤：

1. 创建两个用于运行基本网站的 VM：一个位于“美国东部”，另一个位于“西欧”。
2. 在每个 VM 上安装一个 IIS 服务器。 更新默认网页，该网页描述当用户访问网站时要连接到的 VM 名称。

#### <a name="create-vms-for-running-websites"></a>创建用于运行网站的 VM

在此部分，请在“美国东部”和“西欧”Azure 区域创建两个 VM（*myIISVMEastUS* 和 *myIISVMWestEurope*）。

1. 在 Azure 门户的左上角选择“创建资源” > “计算” > “Windows Server 2019 Datacenter”    。
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择以下值：  

   - **订阅** > **资源组**：选择“新建”，然后键入 **myResourceGroupTM1**。 
   - **实例详细信息** > **虚拟机名称**：键入 *myIISVMEastUS*。
   - “实例详细信息” > “区域”：   选择“美国东部”  。
   - “管理员帐户”   >   “用户名”：输入所选用户名。
   - “管理员帐户”   >   “密码”：输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。
   - “入站端口规则”   >   “公共入站端口”：选择“允许所选端口”  。
   - “入站端口规则”   >   “选择入站端口”：在下拉框中选择“RDP”和“HTTP”。  

3. 选择“管理”选项卡，或者选择“下一步:  **磁盘”，然后选择“下一步:**  网络”，然后选择“下一步:  管理”。 在“监视”  下，将“启动诊断”  设置为“关闭”。 
4. 选择“查看 + 创建”  。
5. 查看设置，并单击“创建”。   
6. 按步骤创建另一个 VM，其名称为 *myIISVMWestEurope*，其“资源组”名称为 *myResourceGroupTM2*，其“位置”为“西欧”，所有其他设置与 *myIISVMEastUS* 相同。   
7. 创建 VM 可能需要数分钟的时间。 在两个 VM 完成创建之前，不要继续执行剩余的步骤。

![创建 VM](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-webpage"></a>安装 IIS 并自定义默认网页

在此部分，请在 myIISVMEastUS 和 myIISVMWestEurope 这两个 VM 上安装 IIS 服务器，然后更新默认网页。 自定义网页显示从 Web 浏览器访问网站时要连接到的 VM 的名称。

1. 在左侧菜单中选择“所有资源”  。 从资源列表选择“myResourceGroupTM1”资源组中的“myIISVMEastUS”。  
2. 在“概览”页上，选择“连接”。   在“连接到虚拟机”中选择“下载 RDP 文件”。  
3. 打开下载的 .rdp 文件。 出现提示时，选择“连接”  。 输入创建 VM 时指定的用户名和密码。 可能需要选择“更多选择”   > “使用其他帐户”  ，以指定在创建 VM 时输入的凭据。
4. 选择“确定”  。
5. 你可能会在登录过程中收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接。  
6. 在服务器桌面上浏览到“Windows 管理工具” > “服务器管理器”。  
7. 在 VM1 上打开 Windows PowerShell。 使用以下命令安装 IIS 服务器并更新默认的 .htm 文件。

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![安装 IIS 并自定义网页](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. 关闭与 **myIISVMEastUS** 之间的 RDP 连接。
9. 重复步骤 1-8。 与 **myResourceGroupTM2** 资源组中的 VM **myIISVMWestEurope** 建立 RDP 连接，以安装 IIS 并自定义其默认网页。

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>为运行 IIS 的 VM 配置 DNS 名称

流量管理器基于服务终结点的 DNS 名称路由用户流量。 在此部分，请为 IIS 服务器 myIISVMEastUS 和 myIISVMWestEurope 配置 DNS 名称。

1. 在左侧菜单中选择“所有资源”  。 从资源列表选择“myResourceGroupTM1”资源组中的“myIISVMEastUS”。  
2. 在“概述”页上的“DNS 名称”下，选择“配置”。   
3. 在“配置”页上的 DNS 名称标签下添加唯一的名称。  再选择“保存”  。
4. 针对 **myResourceGroupTM2** 资源组中名为 **myIISVMWestEurope** 的 VM 重复步骤 1-3。

### <a name="create-a-test-vm"></a>创建测试 VM

在本部分，我们将在每个 Azure 区域（“美国东部”和“西欧”）各创建一个 VM（*myVMEastUS* 和 *myVMWestEurope*）。   将使用这些 VM 来测试流量管理器如何将流量路由到其权重值更高的网站终结点。

1. 在 Azure 门户的左上角选择“创建资源” > “计算” > “Windows Server 2019 Datacenter”    。
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择以下值：  

   - **订阅** > **资源组**：选择“myResourceGroupTM1”  。
   - **实例详细信息** > **虚拟机名称**：键入 *myVMEastUS*。
   - “实例详细信息” > “区域”：   选择“美国东部”  。
   - “管理员帐户”   >   “用户名”：输入所选用户名。
   - “管理员帐户”   >   “密码”：输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。
   - “入站端口规则”   >   “公共入站端口”：选择“允许所选端口”  。
   - “入站端口规则”   >   “选择入站端口”：在下拉框中选择“RDP”。 

3. 选择“管理”选项卡，或者选择“下一步:  **磁盘”，然后选择“下一步:**  网络”，然后选择“下一步:  管理”。 在“监视”  下，将“启动诊断”  设置为“关闭”。 
4. 选择“查看 + 创建”  。
5. 查看设置，并单击“创建”。   
6. 按步骤创建另一个 VM，其名称为 *myVMWestEurope*，其“资源组”名称为 *myResourceGroupTM2*，其“位置”为“西欧”，所有其他设置与 *myVMEastUS* 相同。   
7. 创建 VM 可能需要数分钟的时间。 在两个 VM 完成创建之前，不要继续执行剩余的步骤。

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

根据**权重**路由方法创建流量管理器配置文件。

1. 在屏幕左上方，选择“创建资源”   > “网络”   >   “流量管理器配置文件” >   “创建”。
2. 在“创建流量管理器配置文件”中，输入或选择以下信息  。 对于其他设置，请接受默认值，然后选择“创建”。 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | Name                   | 输入 trafficmanager.net 区域中的唯一名称。 它会生成 DNS 名称 trafficmanager.net，用于访问流量管理器配置文件。                                   |
    | 路由方法          | 选择**权重**路由方法。                                       |
    | Subscription            | 选择订阅。                          |
    | Resource group          | 选择“使用现有”，然后选择“myResourceGroupTM1”   。 |
    |        |   |

    ![创建流量管理器配置文件](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

添加两个运行 IIS 服务器的 VM（myIISVMEastUS 和 myIISVMWestEurope），以便将用户流量路由到其中。

1. 在门户的搜索栏中，搜索在上一部分创建的流量管理器配置文件名称。 在显示的结果中选择配置文件。
2. 在“流量管理器配置文件”  的“设置”  部分，选择“终结点”   >   “添加”。
3. 输入或选择以下信息。 对于其他设置，请接受默认值，然后选择“确定”。 

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 类型                    | 输入 Azure 终结点。                                   |
    | Name           | 输入 **myEastUSEndpoint**。                                        |
    | 目标资源类型           | 选择“公共 IP 地址”。                           |
    | 目标资源          | 选择一个公共 IP 地址，以显示同一订阅下具有公共 IP 地址的资源的列表。 在“资源”中，选择名为 **myIISVMEastUS-ip** 的公共 IP 地址。  这是美国东部的 IIS 服务器 VM 的公共 IP 地址。|
    |  重量      | 输入 **100**。        |
    |        |           |

4. 重复步骤 2 和 3，为公共 IP 地址 **myIISVMWestEurope-ip** 添加名为 **myWestEuropeEndpoint** 的另一个终结点。 此地址与名为 myIISVMWestEurope 的 IIS 服务器 VM 相关联。 输入 **25** 作为“权重”。 
5. 添加完这两个终结点后，它们会显示在流量管理器配置文件中，其监视状态为“联机”。 

## <a name="test-the-traffic-manager-profile"></a>测试流量管理器配置文件

若要查看流量管理器的运作方式，请完成以下步骤：

1. 确定流量管理器配置文件的 DNS 名称。
2. 查看正在运行的流量管理器。

### <a name="determine-dns-name-of-traffic-manager-profile"></a>确定流量管理器配置文件的 DNS 名称

在本教程中，为简单起见，我们将使用流量管理器配置文件的 DNS 名称来访问网站。

可按如下所述确定流量管理器配置文件的 DNS 名称：

1. 在门户的搜索栏中，搜索在上一部分创建的流量管理器配置文件名称。 在显示的结果中，选择流量管理器配置文件。
2. 选择“概述”。 
3. 流量管理器配置文件会显示其 DNS 名称 在生产部署中，请使用 DNS CNAME 记录配置一个指向流量管理器域名的虚构域名。

   ![流量管理器 DNS 名称](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>查看正在运行的流量管理器

在此部分，可以查看流量管理器的运作方式。

1. 在左侧菜单中选择“所有资源”  。 从资源列表选择“myResourceGroupTM1”资源组中的“myVMEastUS”。  
2. 在“概览”页上，选择“连接”。   在“连接到虚拟机”中选择“下载 RDP 文件”。  
3. 打开下载的 .rdp 文件。 出现提示时，选择“连接”  。 输入在创建 VM 时指定的用户名和密码。 可能需要选择“更多选择”   > “使用其他帐户”  ，以指定在创建 VM 时输入的凭据。
4. 选择“确定”  。
5. 你可能会在登录过程中收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接。  
6. 在 VM myVMEastUS 上的 Web 浏览器中，输入流量管理器配置文件的 DNS 名称，以查看网站。 系统会将你路由到托管在 IIS 服务器 myIISVMEastUS 上的网站，因为为它分配的权重较高，为 **100**。 为 IIS 服务器 myIISVMWestEurope 分配的终结点权重值较低，为 **25**。

   ![测试流量管理器配置文件](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. 在 VM myVMWestEurope 上重复步骤 1-6，看看加权网站的响应。

## <a name="delete-the-traffic-manager-profile"></a>删除流量管理器配置文件

不再需要本教程中创建的资源组时，可将其删除。 为此，请选择资源组（**ResourceGroupTM1** 或 **ResourceGroupTM2**），然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [根据用户的地理位置将流量路由到特定的终结点](traffic-manager-configure-geographic-routing-method.md)
