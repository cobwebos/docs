---
title: "Azure 外围网络示例 – 使用 NSG 构建简单的外围网络 | Microsoft 文档"
description: "使用网络安全组 (NSG) 构建外围网络"
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
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>示例 1 – 使用 NSG 和 Azure Resource Manager 模板构建简单的外围网络
[返回安全边界最佳实践页面][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager 模板](virtual-networks-dmz-nsg.md)
> * [经典 - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

本示例创建一个基本的外围网络，其中包含四个 Windows 服务器和网络安全组。 本示例将描述每个相关模板部分，让读者更加深入地了解每个步骤。 另外还提供了“流量方案”部分，让读者逐步深入了解流量如何流经外围网络的各个防御层。 最后的参考部分提供了完整的模板代码，说明如何构建此环境来测试和试验各种方案。 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![使用 NSG 的入站外围网络][1]

## <a name="environment-description"></a>环境描述
本示例中的订阅包含以下资源：

* 单个资源组
* 包含以下两个子网的虚拟网络：“FrontEnd”和“BackEnd”
* 应用到这两个子网的网络安全组
* 一个代表应用程序 Web 服务器的 Windows Server（“IIS01”）
* 两个代表应用程序后端服务器的 Windows Server（“AppVM01”、“AppVM02”）
* 一个代表 DNS 服务器的 Windows Server（“DNS01”）
* 与应用程序 Web 服务器关联的公共 IP 地址

在参考部分，提供了一个用于构建本示例中所述的环境的 Azure Resource Manager 模板的链接。 尽管该示例模板还完成了 VM 和虚拟网络的构建，但本文档未对其进行详细描述。 

**若要构建此环境**，请参阅本文档参考部分中的详细说明；

1. 通过以下链接部署 Azure Resource Manager 模板：[Azure 快速启动模板][Template]
2. 通过以下链接安装示例应用程序：[示例应用程序脚本][SampleApp]

>[!NOTE]
>为了能够通过 RDP 连接到此实例中的任何后端服务器，我们使用了 IIS 服务器作为“跳接箱”。 首先通过 RDP 连接到 IIS 服务器，然后在 IIS 服务器上通过 RDP 连接到后端服务器。 或者，可将一个公共 IP 与每个服务器 NIC 相关联，方便建立 RDP 连接。
> 
>

以下部分通过演练 Azure Resource Manager 模板的关键代码行，详细说明本示例的网络安全组及其运行方式。

## <a name="network-security-groups-nsg"></a>网络安全组 (NSG)
本示例将构建一个 NSG 组，然后加载六个规则。 

>[!TIP]
>一般而言，应该先创建特定的“允许”规则，最后再创建一般的“拒绝”规则。 分配的优先级确定了要先评估哪些规则。 发现要向流量应用的特定规则后，不再需要评估后续规则。 可以朝入站或出站方向（从子网的角度看）应用 NSG 规则。
>
>

以声明性的方式为入站流量构建以下规则：

1. 允许内部 DNS 流量（端口 53）
2. 允许从 Internet 到任何 VM 的 RDP 流量（端口 3389）
3. 允许从 Internet 到 Web 服务器 (IIS01) 的 HTTP 流量（端口 80）
4. 允许从 IIS01 到 AppVM1 的任何流量（所有端口）
5. 拒绝从 Internet 到整个 VNet（两个子网）的任何流量（所有端口）
6. 拒绝从前端子网到后端子网的任何流量（所有端口）

将这些规则绑定到每个子网后，如果有从 Internet 到 Web 服务器的入站 HTTP 请求，将应用规则 3（允许）和规则 5（拒绝），但由于规则 3 具有较高的优先级，因此只应用规则 3 并忽略规则 5。 这样就会允许 HTTP 请求传往 Web 服务器。 如果相同的流量尝试传往 DNS01 服务器，则会先应用规则 5（拒绝），因此不允许该流量传递到服务器。 规则 6（拒绝）阻止前端子网与后端子网对话（规则 1 和 4 允许的流量除外），此规则集可在攻击者入侵前端上的 Web 应用程序时保护后端网络，攻击者只能对后端的“受保护”网络进行有限访问（只能访问 AppVM01 服务器上公开的资源）。

有一个默认出站规则可允许流量外流到 Internet。 在本示例中，我们允许出站流量，且未修改任何出站规则。 要向两个方向的流量应用安全策略，需要使用用户定义的路由，[安全边界最佳实践页][HOME]中的“示例 3”将对此进行介绍。

下面更详细介绍了每个规则：

1. 必须实例化网络安全组资源，使其能够保存规则：

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

2. 本示例中的第一个规则允许所有内部网络之间的 DNS 流量发往后端子网上的 DNS 服务器。 该规则有一些重要参数：
  * “destinationAddressPrefix”- 规则可以使用一种特殊类型的、称作“默认标记”的地址前缀。这些标记是系统提供的标识符，用于方便对较大类别的地址前缀进行寻址。 此规则使用默认标记“Internet”来表示 VNet 外部的任何地址。 其他前缀标签包括 VirtualNetwork 和 AzureLoadBalancer。
  * “方向”表示此规则要对哪个方向的流量生效。 该方向是从子网或虚拟机的角度定义的（取决于此 NSG 绑定到的位置）。 因此，如果“方向”为“入站”并且流量进入子网，此规则将适用，而离开子网的流量则不受此规则影响。
  * “优先级”设置流量的评估顺序。 编号越低，优先级就越高。 将某个规则应用于特定的流量后，不再处理其他规则。 因此，如果优先级为 1 的规则允许流量，优先级为 2 的规则拒绝流量，并将这两个规则同时应用于流量，则允许流量流动（规则 1 的优先级更高，因此将发生作用，并且不再应用其他规则）。
  * “访问”表示是要阻止（“拒绝”）还是允许（“允许”）受此规则影响的流量。

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

3. 此规则允许 RDP 流量从 Internet 发往绑定的子网中任一服务器上的 RDP 端口。 

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

4. 此规则允许入站 Internet 流量抵达 Web 服务器。 此规则不会更改路由行为， 只允许发往 IIS01 的流量通过。 因此，如果来自 Internet 的流量将 Web 服务器作为其目标，此规则将允许流量，并停止处理其他规则。 （在优先级为 140 的规则中，其他所有入站 Internet 流量均被阻止）。 如果只要处理 HTTP 流量，可将此规则进一步限制为只允许目标端口 80。

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

5. 此规则允许流量从 IIS01 服务器传递到 AppVM01 服务器，后面的规则将阻止其他所有从前端到后端的流量。 如果要添加的端口是已知的，则可以改善此规则。 例如，如果 IIS 服务器只抵达 AppVM01 上的 SQL Server，并且 Web 应用程序曾遭到入侵，则目标端口范围应该从“*”（任何）更改为 1433（SQL 端口），以缩小 AppVM01 上的入站攻击面。

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

6. 此规则将拒绝从 Internet 到网络上任何服务器的流量。 使用优先级为 110 和 120 的规则的效果是，只允许入站 Internet 流量发往服务器上的防火墙和 RDP 端口，除此之外的其他流量会被阻止。 此规则是一种“故障安全性”规则，可阻止所有意外的流量。

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

7. 最后一个规则拒绝从前端子网到后端子网的流量。 由于这是仅限入站的规则，因此允许反向流量（从后端到前端）。

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

## <a name="traffic-scenarios"></a>流量方案
#### <a name="allowed-internet-to-web-server"></a>（*允许*）从 Internet 访问 Web 服务器
1. Internet 用户从与 IIS01 NIC 关联的 NIC 的公共 IP 地址请求 HTTP 页面
2. 公共 IP 地址将流量传递到 VNet，然后转发到 IIS01（Web 服务器）
3. 前端子网开始处理入站规则：
  1. NSG 规则 1 (DNS) 不适用，会转到下一规则
  2. NSG 规则 2 (RDP) 不适用，会转到下一规则
  3. NSG 规则 3（Internet 到 IIS01）适用，允许流量，停止处理规则
4. 流量抵达 Web 服务器 IIS01 的内部 IP 地址 (10.0.1.5)
5. IIS01 正在侦听 Web 流量，将接收此请求并开始处理请求
6. IIS01 请求 AppVM01 上的 SQL Server 提供信息
7. 前端子网上没有出站规则，允许流量
8. 后端子网开始处理入站规则：
  1. NSG 规则 1 (DNS) 不适用，会转到下一规则
  2. NSG 规则 2 (RDP) 不适用，会转到下一规则
  3. NSG 规则 3（Internet 到防火墙）不适用，会转到下一规则
  4. NSG 规则 4（IIS01 到 AppVM01）适用，允许流量，停止规则处理
9. AppVM01 接收 SQL 查询并做出响应
10. 由于后端子网上没有出站规则，因此允许响应
11. 前端子网开始处理入站规则：
  1. 后端子网到前端子网的入站流量没有适用的 NSG 规则，因此不会应用任何 NSG 规则
  2. 允许子网间流量的默认系统规则允许此流量，因此允许流量。
12. IIS 服务器接收 SQL 响应，完成 HTTP 响应并发送给请求方
13. 前端子网上没有出站规则，因此允许响应，Internet 用户将收到请求的网页。

#### <a name="allowed-rdp-to-iis-server"></a>（*允许*）通过 RDP 连接到 IIS 服务器
1. Internet 上的服务器管理员请求与 NIC 公共 IP 地址上的 IIS01 建立 RDP 会话，此 NIC 与 IIS01 NIC 关联（可通过门户或 PowerShell 找到此公共 IP 地址）
2. 公共 IP 地址将流量传递到 VNet，然后转发到 IIS01（Web 服务器）
3. 前端子网开始处理入站规则：
  1. NSG 规则 1 (DNS) 不适用，会转到下一规则
  2. NSG 规则 2 (RDP) 适用，允许流量，停止规则处理
4. 由于没有出站规则，将应用默认规则并允许返回流量
5. 已启用 RDP 会话
6. IIS01 提示输入用户名和密码

>[!NOTE]
>为了能够通过 RDP 连接到此实例中的任何后端服务器，我们使用了 IIS 服务器作为“跳接箱”。 首先通过 RDP 连接到 IIS 服务器，然后在 IIS 服务器上通过 RDP 连接到后端服务器。
>
>

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>（*允许*）在 DNS 服务器上执行 Web 服务器 DNS 查找
1. Web 服务器 IIS01 需要 www.data.gov 上的数据源，但需要解析地址。
2. VNet 的网络配置将 DNS01（后端子网上的 10.0.2.4）列为主 DNS 服务器，IIS01 将 DNS 请求发送到 DNS01
3. 前端子网上没有出站规则，允许流量
4. 后端子网开始处理入站规则：
  * NSG 规则 1 (DNS) 适用，允许流量，停止规则处理
5. DNS 服务器接收请求
6. DNS 服务器没有缓存的地址，请求 Internet 上的根 DNS 服务器
7. 后端子网上没有出站规则，允许流量
8. Internet DNS 服务器做出响应，由于此会话是从内部发起的，因此允许响应
9. DNS 服务器缓存响应，然后将初始请求响应发送给 IIS01
10. 后端子网上没有出站规则，允许流量
11. 前端子网开始处理入站规则：
  1. 后端子网到前端子网的入站流量没有适用的 NSG 规则，因此不会应用任何 NSG 规则
  2. 允许子网间流量的默认系统规则允许此流量，因此允许流量
12. IIS01 从 DNS01 接收响应

#### <a name="allowed-web-server-access-file-on-appvm01"></a>（*允许*）Web 服务器访问 AppVM01 上的文件
1. IIS01 请求 AppVM01 上的文件
2. 前端子网上没有出站规则，允许流量
3. 后端子网开始处理入站规则：
  1. NSG 规则 1 (DNS) 不适用，会转到下一规则
  2. NSG 规则 2 (RDP) 不适用，会转到下一规则
  3. NSG 规则 3（Internet 到 IIS01）不适用，会转到下一规则
  4. NSG 规则 4（IIS01 到 AppVM01）适用，允许流量，停止规则处理
4. AppVM01 接收请求并以文件做出响应（假设已获得访问授权）
5. 由于后端子网上没有出站规则，因此允许响应
6. 前端子网开始处理入站规则：
  1. 后端子网到前端子网的入站流量没有适用的 NSG 规则，因此不会应用任何 NSG 规则
  2. 允许子网间流量的默认系统规则允许此流量，因此允许流量。
7. IIS 服务器接收文件

#### <a name="denied-rdp-to-backend"></a>（*拒绝*）通过 RDP 连接到后端
1. Internet 用户尝试通过 RDP 连接到服务器 AppVM01
2. 由于没有公共 IP 地址与此服务器 NIC 关联，此流量永远不会进入 VNet，也不会到达服务器
3. 但是，如果出于某种原因启用了公共 IP 地址，NSG 规则 2 (RDP) 将允许此流量

>[!NOTE]
>为了能够通过 RDP 连接到此实例中的任何后端服务器，我们使用了 IIS 服务器作为“跳接箱”。 首先通过 RDP 连接到 IIS 服务器，然后在 IIS 服务器上通过 RDP 连接到后端服务器。
>
>

#### <a name="denied-web-to-backend-server"></a>（*拒绝*）通过 Web 访问后端服务器
1. Internet 用户尝试访问 AppVM01 上的文件
2. 由于没有公共 IP 地址与此服务器 NIC 关联，此流量永远不会进入 VNet，也不会到达服务器
3. 如果出于某种原因启用了公共 IP 地址，NSG 规则 5（Internet 到 VNet）将阻止此流量

#### <a name="denied-web-dns-look-up-on-dns-server"></a>（*拒绝*）在 DNS 服务器上执行 Web DNS 查找
1. Internet 用户尝试查找 DNS01 上的内部 DNS 记录
2. 由于没有公共 IP 地址与此服务器 NIC 关联，此流量永远不会进入 VNet，也不会到达服务器
3. 如果出于某种原因启用了公共 IP 地址，NSG 规则 5（Internet 到 VNet）将阻止此流量（注意：将不应用规则 1 (DNS)，因为请求源地址是 Internet，而规则 1 只适用于以本地 VNet 作为源的情况）

#### <a name="denied-sql-access-on-the-web-server"></a>（*拒绝*）在 Web 服务器上进行 SQL 访问
1. Internet 用户从 IIS01 请求 SQL 数据
2. 由于没有公共 IP 地址与此服务器 NIC 关联，此流量永远不会进入 VNet，也不会到达服务器
3. 如果出于某种原因启用了公共 IP 地址，前端子网将开始处理入站规则：
  1. NSG 规则 1 (DNS) 不适用，会转到下一规则
  2. NSG 规则 2 (RDP) 不适用，会转到下一规则
  3. NSG 规则 3（Internet 到 IIS01）适用，允许流量，停止处理规则
4. 流量抵达 IIS01 的内部 IP 地址 (10.0.1.5)
5. IIS01 未侦听端口 1433，因此不会对请求做出响应

## <a name="conclusion"></a>结束语
本示例是一种隔离后端子网与输入流量的方式，相当直截了当。

可以在[此处][HOME]找到更多示例和网络安全边界的概述。

## <a name="references"></a>参考
### <a name="azure-resource-manager-template"></a>Azure Resource Manager 模板
本示例使用 Microsoft 维护的、向社区开放的 GitHub 存储库的预定义 Azure Resource Manager 模板。 可直接从 GitHub 部署此模板，或者根据需要将其下载并进行修改。 

主模板位于名为“azuredeploy.json”的文件中。 可通过 PowerShell 或 CLI 提交此模板（结合关联的“azuredeploy.parameters.json”文件），以部署此模板。 最简单的方法就是使用 GitHub 上的 README.md 页面中的“部署到 Azure”按钮。

若要从 GitHub 和 Azure 门户部署用于构建本示例的模板，请执行以下步骤：

1. 在浏览器中，导航到[模板][Template]
2. 单击“部署到 Azure”按钮（或单击“可视化”按钮查看此模板的图形表示形式）
3. 在“参数”边栏选项卡中输入“存储帐户”、“用户名”和“密码”，并单击“确定”
5. 为此部署创建资源组（可以使用现有的资源组，但建议新建一个，以获得最佳效果）
6. 如有必要，请更改 VNet 的“订阅”和“位置”设置。
7. 单击“查看法律条款”，阅读条款，并单击“购买”表示同意条款。
8. 单击“创建”开始部署此模板。
9. 部署成功完成后，导航到为此部署创建的资源组，查看该资源组中配置的资源。

>[!NOTE]
>使用此模板只能通过 RDP 连接到 IIS01 服务器（可在门户中找到 IIS01 的公共 IP）。 为了能够通过 RDP 连接到此实例中的任何后端服务器，我们使用了 IIS 服务器作为“跳接箱”。 首先通过 RDP 连接到 IIS 服务器，然后在 IIS 服务器上通过 RDP 连接到后端服务器。
>
>

若要删除此部署，请删除资源组，这会同时所有子资源。

#### <a name="sample-application-scripts"></a>示例应用程序脚本
成功运行模板后，可以设置 Web 服务器，此外还可以设置应用服务器并在其中包含一个简单的 Web 应用程序，以便使用此外围网络配置进行测试。 若要为此安装示例应用程序和其他外围网络示例，以下链接提供了所需的示例：[应用程序脚本示例][SampleApp]

## <a name="next-steps"></a>后续步骤

* 部署本示例
* 构建示例应用程序
* 测试不同的流量流经此外围网络的情况

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "使用 NSG 的入站外围网络"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md