---
title: "Azure 的外围网络示例 – 构建简单的外围网络与 Nsg |Microsoft 文档"
description: "构建外围网络与网络安全组 (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>示例 1 – 构建简单的外围网络与 Azure 资源管理器模板使用 Nsg
[返回到安全边界最佳实践页][HOME]

> [!div class="op_single_selector"]
> * [资源管理器模板](virtual-networks-dmz-nsg.md)
> * [经典-PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

此示例将创建具有四个 Windows 服务器和网络安全组基元外围网络。 本示例介绍了每个相关的模板部分提供每个步骤的更深入了解。 此外还有一个流量方案部分，以便提供深入分步探讨了如何通过在外围网络层进行流量。 最后，在引用中部分是完整的模板代码和说明，生成此环境来测试和试验各种方案。 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![使用 NSG 的入站外围网络][1]

## <a name="environment-description"></a>环境描述
在此示例订阅包含以下资源：

* 单个资源组
* 包含两个子网; 的虚拟网络"FrontEnd"和"BackEnd"
* 应用于这两个子网的网络安全组
* 表示应用程序 web 服务器 ("IIS01") 的 Windows Server
* 表示应用程序后端服务器 （"AppVM01"、"AppVM02"） 的两个 windows 服务器
* Windows server 表示 DNS 服务器 ("DNS01") 的文件
* 与应用程序 web 服务器关联的公共 IP 地址

在引用部分中，没有生成在此示例中所述的环境的 Azure 资源管理器模板的链接。 生成虚拟机和虚拟网络，尽管示例模板中，通过完成中未说明本文档中的详细信息。 

**若要生成此环境**（详细的说明都在本文档的引用部分）;

1. 部署 Azure 资源管理器模板： [Azure 快速入门模板][Template]
2. 安装在该示例应用程序：[示例应用程序脚本][SampleApp]

>[!NOTE]
>到此实例中任何后端服务器的 RDP，将使用的 IIS 服务器作为"跳转盒"。 第一个 RDP 到 IIS 服务器，然后从 IIS 服务器 RDP 到后端服务器。 或者公共 IP 可以与每个服务器更容易 rdp NIC 相关联。
> 
>

以下部分提供网络安全组和它的工作方式对于此示例通过浏览键行的 Azure 资源管理器模板的详细的的说明。

## <a name="network-security-groups-nsg"></a>网络安全组 (NSG)
例如，一个 NSG 组生成，然后加载六个规则。 

>[!TIP]
>通常情况下，应首先创建特定的"允许"规则，然后更通用的"拒绝"规则的最后一。 分配的优先级决定哪个规则是对第一个求值。 一旦流量找到要应用于特定规则，不评估任何其他规则。 NSG 规则可以朝入站或出站方向 （从子网的角度来看） 应用。
>
>

以声明方式为入站流量构建以下规则：

1. 允许内部 DNS 流量 （端口 53）
2. 允许 RDP 流量 （端口 3389） 从 Internet 到任何 VM
3. 允许 HTTP 流量 （端口 80） 从 Internet 到 web 服务器 (IIS01)
4. 允许的任何流量 （所有端口） 从 IIS01 到 AppVM1
5. 拒绝的任何流量 （所有端口） 从 Internet 到整个 VNet （两个子网）
6. 拒绝的任何流量 （所有端口） 从前端子网到后端子网

这些规则绑定到每个子网，如果是从 Internet 到 web 服务器，这两个规则 3 入站 HTTP 请求 （允许） 和 5 （拒绝），但由于规则 3 具有较高优先级，只会应用和规则 5 将不起作用。 这样到 web 服务器就会允许 HTTP 请求。 如果相同的流量尝试传往 DNS01 服务器，会先应用规则 5 （拒绝）。 并且不会允许流量传递到服务器。 规则 6 （拒绝） 阻止前端子网与后端子网 （规则 1 和 4 中允许的流量） 除外，此规则集保护用例中的后端网络上的前端，攻击者的 web 应用程序将具有有限的访问 （仅对 AppVM01 服务器上公开的资源） 受后端"保护"的网络攻击者破坏。

没有默认出站规则可允许流量外流到 internet。 对于此示例，我们要允许出站流量，且不修改任何出站规则。 若要应用安全策略，在两个方向的流量，用户定义的路由是必需的在"示例 3"上进行介绍[安全边界最佳实践页][HOME]。

每个规则已在更多详细信息，如下所示：

1. 必须实例化的网络安全组资源，以保存规则：

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. 在此示例中的第一个规则允许到后端子网上的 DNS 服务器的所有内部网络之间的 DNS 流量。 规则中有一些重要参数：
  * "destinationAddressPrefix"的规则可以使用一种特殊类型的称为"默认标记"的地址前缀，这些标记是系统提供的更方便地进行寻址的地址前缀的更大类别的标识符。 此规则使用"Internet"的默认标记来表示任何外部 VNet 的地址。 其他前缀标签是 VirtualNetwork 和 AzureLoadBalancer。
  * "方向"表示此规则将生效，通信流的方向。 方向是从子网或虚拟机的角度 （具体取决于其中绑定此 NSG）。 因此如果方向为"Inbound"以及流量输入子网，规则将会应用和离开子网的流量将不受此规则。
  * "优先级"设置通信流的计算顺序。 越低的数字的优先级就越高。 当规则应用到特定通信流时，没有进一步的规则进行处理。 因此如果使用优先级为 1 的规则允许流量，并具有优先级为 2 的规则拒绝流量，和这两个规则应用到流量然后将允许该流量流动 （因为规则 1 具有更高优先级所花费的效果和没有进一步的规则应用）。
  * "访问"表示此规则影响的流量是否被阻止 ("Deny") 或允许 （"允许"）。

    ```JSON
    "properties": {
    "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
    ```

3. 此规则允许从 internet 流向绑定子网上的任何服务器上的 RDP 端口的 RDP 流量。 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. 此规则允许入站的 internet 流量要进行命中 web 服务器。 此规则不会更改路由行为。 该规则仅允许发往 IIS01 传递流量。 因此如果来自 Internet 的流量将 web 服务器必须作为其目标，此规则将允许它并停止进一步处理规则。 （在优先级规则中 140 所有其他入站的 internet 流量被阻止）。 如果您仅在处理 HTTP 流量，此规则无法进一步限制为仅允许目标端口 80。

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. 此规则允许流量传递从 IIS01 服务器到 AppVM01 服务器，更高版本的规则块所有其他前端到后端流量。 若要提高此规则，如果端口已知的它应添加。 例如，如果 IIS 服务器达到仅 AppVM01 上的 SQL Server，目标端口范围应更改从"*"（任何） 到 1433 （SQL 端口），从而允许 web 应用程序不断在受到威胁的较小的 AppVM01 入站的攻击面。

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. 对网络上的任何服务器情况下，此规则拒绝从 internet 的流量。 通过在优先级 110 和 120 规则，效果是仅允许入站的 internet 流量的防火墙和服务器和块上的 RDP 端口其他所有内容。 此规则是"防故障"规则以阻止所有意外的流。

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. 最终规则拒绝流量从前端子网到后端子网。 由于此规则是唯一的入站的规则，（从前端到后端） 允许反向流量。

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>通信方案
#### <a name="allowed-internet-to-web-server"></a>(*允许*) Internet 到 web 服务器
1. 从与 IIS01 NIC 关联的 NIC 的公共 IP 地址的 internet 用户请求的 HTTP 页
2. 公共 IP 地址将流量传递到朝向 IIS01 VNet （web 服务器）
3. 前端子网开始入站的规则处理：
  1. NSG 规则 1 (DNS) 不适用，将移到下一个规则
  2. NSG 规则 2 (RDP) 不适用，将移到下一个规则
  3. NSG 规则 3 (IIS01 到 Internet) 不适用于、 流量是一种允许，停止规则处理
4. 流量抵达内部 IP 地址的 web 服务器 IIS01 (10.0.1.5)
5. IIS01 侦听 web 流量，接收此请求，然后开始处理请求
6. IIS01 要求在 AppVM01 上 SQL Server 提供信息
7. 前端子网没有出站规则，允许流量
8. 后端子网开始入站的规则处理：
  1. NSG 规则 1 (DNS) 不适用，将移到下一个规则
  2. NSG 规则 2 (RDP) 不适用，将移到下一个规则
  3. NSG 规则 3 (Internet 到防火墙) 不适用，将移到下一个规则
  4. NSG 规则 4 (IIS01 到 AppVM01) 不适用于，允许流量，停止规则处理
9. AppVM01 收到 SQL 查询和响应
10. 由于后端子网中不有任何出站规则，允许响应
11. 前端子网开始入站的规则处理：
  1. 没有适用于入站的 NSG 规则从后端子网到前端子网，因此无 NSG 规则将应用流量
  2. 允许的子网之间的通信的默认系统规则将允许此流量，因此允许该流量。
12. IIS 服务器接收 SQL 响应并完成的 HTTP 响应，将发送到请求者
13. 由于前端子网中不有任何出站规则，允许响应，并且 Internet 用户会收到请求的 web 页面。

#### <a name="allowed-rdp-to-iis-server"></a>(*允许*) 到 IIS 服务器的 RDP
1. Internet 上的服务器管理员请求到 IIS01 上 （通过门户或 PowerShell，就可以找到此公共 IP 地址） IIS01 NIC 与关联的 NIC 的公共 IP 地址的 RDP 会话
2. 公共 IP 地址将流量传递到朝向 IIS01 VNet （web 服务器）
3. 前端子网开始入站的规则处理：
  1. NSG 规则 1 (DNS) 不适用，将移到下一个规则
  2. NSG 规则 2 (RDP) 不适用于、 流量是一种允许，停止规则处理
4. 与任何出站规则，将应用默认规则和允许返回流量
5. 启用 RDP 会话
6. IIS01 会提示你输入用户名和密码

>[!NOTE]
>到此实例中任何后端服务器的 RDP，将使用的 IIS 服务器作为"跳转盒"。 第一个 RDP 到 IIS 服务器，然后从 IIS 服务器 RDP 到后端服务器。
>
>

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*允许*) DNS 服务器上的 Web 服务器 DNS 查找功能
1. Web 服务器，IIS01，需求的数据馈送在 www.data.gov，但需要解析的地址。
2. 网络配置 VNet 列表 DNS01 (10.0.2.4 上后端子网) 作为主 DNS 服务器，则 IIS01 会将 DNS 请求发送到 DNS01
3. 前端子网没有出站规则，允许流量
4. 后端子网开始入站的规则处理：
  * NSG 规则 1 (DNS) 确实应用于应用、 流量是一种允许，停止规则处理
5. DNS 服务器接收请求
6. DNS 服务器不具有缓存的地址和要求在 internet 上的根 DNS 服务器
7. 后端子网上的任何出站规则，不允许流量
8. Internet DNS 服务器响应，因为此会话启动内部，允许响应
9. DNS 服务器缓存的响应，并将响应返回到 IIS01 初始请求
10. 后端子网上的任何出站规则，不允许流量
11. 前端子网开始入站的规则处理：
  1. 没有适用于入站的 NSG 规则从后端子网到前端子网，因此无 NSG 规则将应用流量
  2. 允许的子网之间的通信的默认系统规则将允许此流量，因此允许该流量
12. IIS01 DNS01 接收的响应

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*允许*) 上 AppVM01 的 Web 服务器访问文件
1. IIS01 索要 AppVM01 上的文件
2. 前端子网没有出站规则，允许流量
3. 后端子网开始入站的规则处理：
  1. NSG 规则 1 (DNS) 不适用，将移到下一个规则
  2. NSG 规则 2 (RDP) 不适用，将移到下一个规则
  3. NSG 规则 3 (IIS01 到 Internet) 不适用，将移到下一个规则
  4. NSG 规则 4 (IIS01 到 AppVM01) 不适用于，允许流量，停止规则处理
4. AppVM01 接收请求和响应 （假定来授予访问权限） 的文件
5. 由于后端子网中不有任何出站规则，允许响应
6. 前端子网开始入站的规则处理：
  1. 没有适用于入站的 NSG 规则从后端子网到前端子网，因此无 NSG 规则将应用流量
  2. 允许的子网之间的通信的默认系统规则将允许此流量，因此允许该流量。
7. IIS 服务器接收文件

#### <a name="denied-rdp-to-backend"></a>(*拒绝*) RDP 到后端
1. Internet 用户尝试 RDP 到 AppVM01 服务器
2. 由于没有与此 server NIC 关联公共 IP 地址，此通信将从来不用输入 VNet 和无法到达服务器
3. 但是如果出于某种原因启用了公共 IP 地址，NSG 规则 2 (RDP) 将允许此流量

>[!NOTE]
>到此实例中任何后端服务器的 RDP，将使用的 IIS 服务器作为"跳转盒"。 第一个 RDP 到 IIS 服务器，然后从 IIS 服务器 RDP 到后端服务器。
>
>

#### <a name="denied-web-to-backend-server"></a>(*拒绝*) 到后端服务器的 Web
1. Internet 用户尝试访问 AppVM01 上的文件
2. 由于没有与此 server NIC 关联公共 IP 地址，此通信将从来不用输入 VNet 和无法到达服务器
3. 如果出于某种原因启用了公共 IP 地址，NSG 规则 5 (到 VNet 的 Internet) 会阻止此流量

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*拒绝*) DNS 服务器上的 Web DNS 查找
1. Internet 用户尝试查找 DNS01 上的内部 DNS 记录
2. 由于没有与此 server NIC 关联公共 IP 地址，此通信将从来不用输入 VNet 和无法到达服务器
3. 如果出于某种原因启用了公共 IP 地址，NSG 规则 5 (到 VNet 的 Internet) 会阻止此流量 (注意： 由于请求的源地址将不适用于规则 1 (DNS) 是 internet，规则 1 只适用于作为源的本地 VNet)

#### <a name="denied-sql-access-on-the-web-server"></a>(*拒绝*) web 服务器上的 SQL 访问
1. Internet 用户从 IIS01 请求 SQL 数据
2. 由于没有与此 server NIC 关联公共 IP 地址，此通信将从来不用输入 VNet 和无法到达服务器
3. 如果出于某种原因启用了公共 IP 地址前, 端子网开始入站的规则处理：
  1. NSG 规则 1 (DNS) 不适用，将移到下一个规则
  2. NSG 规则 2 (RDP) 不适用，将移到下一个规则
  3. NSG 规则 3 (IIS01 到 Internet) 不适用于、 流量是一种允许，停止规则处理
4. 流量抵达 IIS01 内部 IP 地址 (10.0.1.5)
5. IIS01 不侦听端口 1433，因此没有响应的请求

## <a name="conclusion"></a>结论
此示例是隔离后端子网与输入流量的相对比较简单，直截了当的方式。

可以找到更多示例和网络安全边界的概述[此处][HOME]。

## <a name="references"></a>参考
### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板
此示例使用由 Microsoft 维护的 GitHub 存储库中的预定义的 Azure 资源管理器模板，并对社区开放。 此模板可直接从 GitHub，部署或下载并修改以满足你的需求。 

主模板位于文件中名为"azuredeploy.json。" 此模板可以提交通过 PowerShell 或 CLI （与关联的"azuredeploy.parameters.json"文件） 部署此模板。 找到的最简单方法是使用 GitHub 上的 README.md 页上的"部署到 Azure"按钮。

部署从 GitHub 和 Azure 门户中生成此示例的模板，请执行以下步骤：

1. 从浏览器中，导航到[模板][Template]
2. 单击"部署到 Azure"按钮 （或"可视化"按钮以查看此模板的图形表示）
3. 在参数边栏选项卡，输入存储帐户、 用户名和密码，然后单击**确定**
5. 创建为此部署的资源组 （你可以使用现有的一个，但建议一个新为获得最佳结果）
6. 如有必要，更改你的 VNet 的订阅和位置设置。
7. 单击**查看法律条款**，阅读条款，然后单击**购买**以表示同意。
8. 单击**创建**若要开始部署此模板。
9. 完成部署成功后，导航到为此部署创建的资源组以查看配置内的资源。

>[!NOTE]
>此模板使 RDP 到仅在 IIS01 服务器 （IIS01 在门户上有关查找公共 IP）。 到此实例中任何后端服务器的 RDP，将使用的 IIS 服务器作为"跳转盒"。 第一个 RDP 到 IIS 服务器，然后从 IIS 服务器 RDP 到后端服务器。
>
>

若要删除此部署，删除资源组和所有子资源也将被都删除。

#### <a name="sample-application-scripts"></a>示例应用程序脚本
一旦成功运行该模板，可以设置 web 服务器和使用简单的 web 应用程序以允许与此外围网络配置测试的应用程序服务器。 若要安装示例应用程序有关，和其他外围网络示例，已提供了一个在以下链接：[示例应用程序脚本][SampleApp]

## <a name="next-steps"></a>后续步骤

* 部署此示例
* 生成示例应用程序
* 测试此外围网络的不同流量流经

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "使用 NSG 的入站外围网络"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md