---
title: 在 Azure 上部署以地庭防水功能联合体解决方案模板
description: 使用以地庭机构证明联合体解决方案在 Azure 上部署和配置多成员联合公司以非坊网络
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387422"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>在 Azure 上部署以地庭验证授权联合体解决方案模板

您可以使用[以天地验证机构联盟预览 Azure 解决方案模板](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum)，以最少的 Azure 和以天图知识部署、配置和管理多成员联合体复位机构以地庭网络。

每个联合体成员可以使用解决方案模板使用 Microsoft Azure 计算、网络和存储服务来预配区块链网络占用空间。 每个联联成员的网络占用空间由一组负载平衡验证器节点组成，应用程序或用户可以与之交互以提交以非定器事务。

## <a name="choose-an-azure-blockchain-solution"></a>选择 Azure 区块链解决方案

在选择使用以非坊验证权威联合体解决方案模板之前，请将您的方案与可用 Azure 区块链选项的常见用例进行比较。

选项 | 服务模型 | 常见用例
-------|---------------|-----------------
解决方案模板 | IaaS | 解决方案模板是 Azure 资源管理器模板，可用于预配完全配置的区块链网络拓扑。 这些模板为给定的区块链网络类型部署和配置 Microsoft Azure 计算、网络和存储服务。
[Azure 区块链服务](../service/overview.md) | PaaS | Azure 区块链服务预览简化了联合体区块链网络的形成、管理和治理。 对于需要 PaaS、联合体管理或合同和事务隐私的解决方案，请使用 Azure 区块链服务。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 和 PaaS | Azure Blockchain Workbench 预览版是 Azure 服务和功能的集合，旨在帮助创建和部署区块链应用程序，以便与其他组织共享业务流程和数据。 使用 Azure 区块链工作台原型设计区块链解决方案或区块链应用程序概念验证。

## <a name="solution-architecture"></a>解决方案体系结构

使用以非坊解决方案模板，可以部署基于单个或多区域的多成员以地庭验证权威联盟网络。

![部署体系结构](./media/ethereum-poa-deployment/deployment-architecture.png)

每个联盟成员部署包括：

* 用于运行 PoA 验证程序的虚拟机
* 用于分发 RPC、对等互连和治理 DApp 请求的 Azure 负载均衡器
* 用于确保验证程序标识安全的 Azure Key Vault
* 用于托管持久性网络信息并协调租用的 Azure 存储
* 用于聚合日志和性能统计信息的 Azure Monitor
* VNet 网关（可选），允许跨专用 VNet 的 VPN 连接

默认情况下，可以通过公共 IP 访问 RPC 和对等互连终结点，以简化订阅和云之间的连接。 对于应用程序级访问控件，可以使用[奇偶校验的权限协定](https://wiki.parity.io/Permissioning)。 支持 VPN 后部署的网络，这些网络利用 VNet 网关进行跨订阅连接。 由于 VPN 和 VNet 部署更为复杂，因此在原型设计解决方案时，您可能希望从公共 IP 模型开始。

Docker 容器用于可靠性和模块化。 Azure 容器注册表用于承载和提供版本映像，作为每个部署的一部分。 容器映像包括：

* 协调器 - 生成标识和治理协定。 在标识存储中存储标识。
* 奇偶校验客户端 - 从标识存储租赁标识。 发现并连接到对等方。
* EthStats 代理 - 通过 RPC 收集本地日志和统计信息，并将信息推送到 Azure 监视器。
* 治理 DApp - 用于与治理协定交互的 Web 界面。

### <a name="validator-nodes"></a>验证器节点

在权威证明协议中，验证程序节点取代了传统的挖矿程序节点。 每个验证器都有一个唯一的以太坊标识，允许它参与块创建过程。 每个联盟成员可以跨五个区域预配两个或更多验证程序节点，用于提供异地冗余。 验证程序节点与其他验证程序节点进行通信以就基础分布式账本的状态达成共识。 为确保网络公平参与，每个联营成员不得使用比网络上的第一个成员更多的验证器。 例如，如果第一个成员部署三个验证器，则每个成员最多只能有三个验证器。

### <a name="identity-store"></a>标识存储区

标识存储部署在每个成员的订阅中，该订阅安全地保存生成的以地庭标识。 对于每个验证器，业务流程容器生成以太坊私钥并将其存储在 Azure 密钥保管库中。

## <a name="deploy-ethereum-consortium-network"></a>部署以地庭联合网络

在本演练中，假设您正在创建一个多方以地图姆联合网络。 以是多党部署的示例：

1. 三个成员分别使用 MetaMask 生成 Ethereum 帐户
1. *成员 A*部署以地庭 PoA，提供以地庭公共地址
1. 成员 A 向成员 B 和成员 C 提供联盟 URL******
1. 成员 B 和成员 C 部署 Ethereum PoA，提供其 Ethereum 公用地址和成员 A 的联盟 URL******
1. 成员 A 投票赞成成员 B 作为管理员****
1. 成员 A 和成员 B 都投票赞成成员 C 作为管理员******

下一节将介绍如何配置网络中的第一个成员的占用空间。

### <a name="create-resource"></a>创建资源

在[Azure 门户](https://portal.azure.com)中，选择在左上角**创建资源**。

选择**区块链** > **以非庭权威证明联盟（预览）。**

### <a name="basics"></a>基础

在 **"基本"** 下，为任何部署指定标准参数的值。

![基础](./media/ethereum-poa-deployment/basic-blade.png)

参数 | 描述 | 示例值
----------|-------------|--------------
创建新网络或加入现有网络 | 您可以创建新的联合体网络或加入预先存在的联合体网络。 加入现有网络需要额外的参数。 | 新建
电子邮件地址 | 部署完成有关部署的信息后，您将收到电子邮件通知。 | 有效的电子邮件地址
VM 用户名 | 部署的每个 VM 的管理员用户名 | 1-64 字母数字字符
身份验证类型 | 对虚拟机进行身份验证的方法。 | 密码
密码 | 部署的每个虚拟机的管理员帐户密码。 所有 VM 最初具有相同的密码。 您可以在预配后更改密码。 | 12-72 个字符 
订阅 | 部署联盟网络的订阅 |
资源组| 部署联盟网络的资源组。 | myResourceGroup
位置 | 资源组的 Azure 区域。 | 美国西部 2

选择“确定”。

### <a name="deployment-regions"></a>部署区域

在 *"部署区域"* 下，指定每个区域的区数和位置数。 最多可以在五个区域中部署。 第一个区域应与 *"基本"* 部分的资源组位置匹配。 对于开发或测试网络，每个成员可以使用单个区域。 对于生产，跨两个或多个区域进行部署，以便获得高可用性。

![部署区域](./media/ethereum-poa-deployment/deployment-regions.png)

参数 | 描述 | 示例值
----------|-------------|--------------
区域数量|部署联盟网络的区域数量| 2
第一个区域 | 部署联盟网络的第一个区域 | 美国西部 2
第二个区域 | 第二个区域部署联合体网络。 当区域数为 2 或更大时，其他区域可见。 | 美国东部 2

选择“确定”。

### <a name="network-size-and-performance"></a>网络规模和性能

在 *"网络大小和性能*"下，指定联合体网络大小的输入。 验证器节点存储大小决定了区块链的潜在大小。 部署后可以更改大小。

![网络规模和性能](./media/ethereum-poa-deployment/network-size-and-performance.png)

参数 | 描述 | 示例值
----------|-------------|--------------
负载均衡验证程序节点数 | 作为网络的一部分预配的验证器节点数。 | 2
验证程序节点存储性能 | 每个已部署的验证器节点的托管磁盘类型。 有关定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/managed-disks/) | 标准 SSD
验证程序节点虚拟机大小 | 用于验证程序节点的虚拟机大小。 有关定价的详细信息，请参阅[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | 标准 D2 v3

虚拟机和存储层会影响网络性能。  使用下表帮助选择成本效益：

虚拟机 SKU|存储层|价格|吞吐量|延迟
---|---|---|---|---
F1|标准 SSD|low|low|high
D2_v3|标准 SSD|中|中|中
F16s|高级 SSD|high|high|low

选择“确定”。

### <a name="ethereum-settings"></a>Ethereum 设置

在*以地庭设置*下，指定与以非坊相关的配置设置。

![Ethereum 设置](./media/ethereum-poa-deployment/ethereum-settings.png)

参数 | 描述 | 示例值
----------|-------------|--------------
联盟成员 ID | 与参与联合网络的每个成员关联的 ID。 它用于配置 IP 地址空间以避免冲突。 对于专用网络，成员 ID 应在同一网络中的不同组织中是唯一的。  即使同一个组织部署到多个区域，也需要一个唯一的成员 ID。 记下此参数的值，因为您需要与其他联接成员共享它，以确保没有冲突。 有效范围为 0 到 255。 | 0
网络 ID | 要部署的 Ethereum 联盟网络的网络 ID。 每个 Ethereum 网络都有自己的网络 ID，其中 1 是公共网络的 ID。 有效范围为 5 到 999，999，999 | 10101010
管理员 Ethereum 地址 | 用于参与 PoA 治理的以里瑟姆帐户地址。 您可以使用 MetaMask 生成以太坊地址。 |
高级选项 | 用于 Ethereum 设置的高级选项 | 启用
使用公共 IP 进行部署 | 如果选择了专用 VNet，则网络将部署在 VNet 网关后面，并删除对等访问。 对于专用 VNet，所有成员必须使用 VNet 网关才能兼容连接。 | 公共 IP
块气体限制 | 网络的起始块气体限制。 | 50000000
区块重新封装时间段（秒） | 网络上没有事务时创建空区块的频率。 较高的频率将加快结束，但会增加存储成本。 | 15
交易权限合同 | 事务权限协定的字节码。 将智能合同部署和执行限制为允许的以地庭帐户列表。 |

选择“确定”。

### <a name="monitoring"></a>监视

监视允许您为网络配置日志资源。 监视代理从网络收集和显示有用的指标和日志，从而能够快速检查网络运行状况或调试问题。

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

参数 | 描述 | 示例值
----------|-------------|--------------
监视 | 启用监视的选项 | 启用
连接到现有的 Azure 监视器日志 | 创建新 Azure 监视器日志实例或联接现有实例的选项 | 新建
位置 | 部署新实例的区域 | 美国东部
现有日志分析工作区 ID（连接到现有 Azure 监视器日志 + 加入现有日志）|现有 Azure 监视器日志实例的工作区 ID||NA
现有日志分析主键（连接到现有 Azure 监视器日志 + 加入现有）|用于连接到现有 Azure 监视器日志实例的主键||NA

选择“确定”。

### <a name="summary"></a>总结

单击摘要以查看指定的输入并运行基本部署前验证。 在部署之前，可以下载模板和参数。

选择 **"创建**"以进行部署。

如果部署包括 VNet 网关，则部署可能需要 45 到 50 分钟。

## <a name="deployment-output"></a>部署输出

部署完成后，可以使用 Azure 门户访问必要的参数。

### <a name="confirmation-email"></a>确认电子邮件

如果您提供电子邮件地址（[基础知识部分](#basics)），将发送电子邮件，其中包含部署信息和指向本文档的链接。

![部署电子邮件](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>门户

成功完成部署并预配所有资源后，您可以查看资源组中的输出参数。

1. 转到门户中的资源组。
1. 选择 **"部署>概述**"。

    ![资源组概述](./media/ethereum-poa-deployment/resource-group-overview.png)

1. 选择**微软-azure-区块链.azure-区块链-ether-...** 部署。
1. 选择"**输出"** 部分。

    ![部署输出](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>扩大联盟

要扩展联盟，首先必须连接物理网络。 如果部署在 VPN 后面，请参阅连接[VNet 网关](#connecting-vnet-gateways)的部分，将网络连接配置为新成员部署的一部分。 部署完成后，使用[治理 DApp](#governance-dapp)成为网络管理员。

### <a name="new-member-deployment"></a>新成员部署

与加入的成员共享以下信息。 这些信息在部署后电子邮件或门户部署输出中找到。

* 联盟数据 URL
* 部署的节点数
* VNet 网关资源 ID（若使用 VPN）

部署成员在使用以下指南部署其网络状态时，应使用相同的以非坊防水机构联盟解决方案模板：

* 选择“加入现有”****
* 选择与网络上的其他成员相同的验证器节点数，以确保公平表示
* 使用相同的管理员以地图姆地址
* 在*以以坊设置*中使用提供的*联盟数据 URL*
* 如果网络的其余部分位于 VPN 后面，请在高级部分下选择**专用 VNet**

### <a name="connecting-vnet-gateways"></a>连接 VNet 网关

仅当使用专用 VNet 部署时，才需要此部分。 如果您使用的是公共 IP 地址，则可以跳过此部分。

对于专用网络，不同的成员通过 VNet 网关连接连接。 在成员可以加入网络并查看事务流量之前，现有成员必须在其 VPN 网关上执行最终配置才能接受连接。 在建立连接之前，联接成员的以天间节点不会运行。 要减少单点故障的可能性，请在联合体中创建冗余网络连接。

部署新成员后，现有成员必须通过设置与新成员的 VNet 网关连接来完成双向连接。 现有成员需要：

* 连接成员的 VNet 网关资源 ID。 请参阅[部署输出](#deployment-output)。
* 共享连接密钥。

现有成员必须运行以下 PowerShell 脚本来完成连接。 您可以使用位于门户右上角导航栏中的 Azure 云外壳。

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>服务监视

您可以按照部署电子邮件中的链接或在部署输出 [OMS_PORTAL_URL] 中查找参数来查找 Azure 监视器门户。

门户首先显示高级网络统计信息和节点概述。

![监控类别](./media/ethereum-poa-deployment/monitor-categories.png)

选择**节点概述**可显示每个节点基础结构统计信息。

![节点统计信息](./media/ethereum-poa-deployment/node-stats.png)

选择**网络统计信息**将显示以地图姆网络统计信息。

![网络统计信息](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

您可以查询监视日志以调查故障或设置阈值警报。 以下查询是您可以在*日志搜索*工具中运行的示例：

由多个验证器查询报告的列表块可用于帮助查找链叉。

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

获取指定验证器节点的平均对等计数，平均超过 5 分钟存储桶。

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>SSH 访问权限

出于安全因素，默认情况下，网络组安全规则拒绝 SSH 端口访问。 要访问 PoA 网络中的虚拟机实例，您需要将以下安全规则更改为 *"允许*"。

1. 转到 Azure 门户中已部署的资源组的 **"概述"** 部分。

    ![ssh 概述](./media/ethereum-poa-deployment/ssh-overview.png)

1. 为要访问的 VM 区域选择**网络安全组**。

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

1. 选择**允许-ssh**规则。

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

1. 更改**操作**以**允许**

    ![ssh 启用允许](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. 选择“保存”。**** 更改可能需要几分钟才能应用。

您可以使用提供的管理员用户名和密码/SSH 密钥，通过 SSH 远程连接到验证器节点的虚拟机。 模板部署输出中列出了访问第一个验证器节点的 SSH 命令。 例如：

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

要获取其他事务节点，将端口号增加一个。

如果部署到多个区域，则将命令更改为该区域中负载均衡器的 DNS 名称或 IP 地址。 要查找其他区域的 DNS 名称或 IP 地址，查找具有命名约定**\*\*\*\*\*-lbpip-reg\#** 的资源并查看其 DNS 名称和 IP 地址属性。

## <a name="azure-traffic-manager-load-balancing"></a>Azure 流量管理器负载均衡

Azure 流量管理器可通过路由不同区域中多个部署间的传入流量，帮助减少故障时间并加快 PoA 网络的响应速度。 内置运行状况检查和自动重新路由有助于确保 RPC 终结点和治理 DApp 的高可用性。 如果已部署到多个区域并且已准备好投入生产，则此功能非常有用。

使用流量管理器通过自动故障转移提高 PoA 网络可用性。 您还可以使用流量管理器通过将最终用户路由到网络延迟最低的 Azure 位置来增加网络响应能力。

如果决定创建流量管理器配置文件，可以使用配置文件的 DNS 名称来访问网络。 将其他联盟成员添加到网络中之后，流量管理器也可用于跨其部署的验证程序进行负载均衡。

### <a name="creating-a-traffic-manager-profile"></a>创建流量管理器配置文件

1. 在[Azure 门户](https://portal.azure.com)中，选择在左上角**创建资源**。
1. 搜索**流量管理器配置文件**。

    ![搜索 Azure 流量管理器](./media/ethereum-poa-deployment/traffic-manager-search.png)

    为配置文件指定唯一名称，并选择用于 PoA 部署的资源组。

1. 选择 **"创建**"以进行部署。

    ![创建流量管理器](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. 部署后，在资源组中选择实例。 访问流量管理器的 DNS 名称可在"概述"选项卡中找到。

    ![查找流量管理器 DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. 选择 **"端点"** 选项卡并选择"**添加**"按钮。
1. 为终结点提供唯一名称。
1. 对于**目标资源类型**，请选择**公共 IP 地址**。
1. 选择第一个区域负载均衡器的公共 IP 地址。

    ![路由流量管理器](./media/ethereum-poa-deployment/traffic-manager-routing.png)

对已部署网络中的每个区域重复此操作。 终结点处于**启用**状态后，在流量管理器的 DNS 名称下自动加载和平衡区域。 现在，您可以在本文的其他步骤中使用此 DNS 名称代替 [CONSORTIUM_DATA_URL] 参数。

## <a name="data-api"></a>数据 API

每个联盟成员都拥有其他成员连接到网络所需的信息。 为了便于连接，每个成员在数据 API 终结点上承载一组连接信息。

现有成员在成员部署之前提供 [CONSORTIUM_DATA_URL] 。 部署后，加入的成员可从以下终结点的 JSON 接口检索信息：

`<CONSORTIUM_DATA_URL>/networkinfo`

响应包含可用于加入成员的信息（Genesis 块、验证器集协定 ABI、引导节点）以及对现有成员有用的信息（验证器地址）。 您可以使用此标准化将联合体扩展到云提供商。 此 API 返回具有以下结构的 JSON 格式化响应：

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>Governance DApp

权威证明的核心是分散式治理。 由于授权证明依赖于允许的网络颁发机构列表来保持网络正常运行，因此提供一个公平的机制来修改此权限列表非常重要。 每个部署都附带一组智能合同和门户，用于此允许列表的链上治理。 一旦提议的更改获得联盟成员的多数票，就会执行更改。 投票允许以透明的方式删除新的共识参与者或妥协的参与者，从而鼓励诚实的网络。

治理 DApp 是一组预先部署的[智能合约](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts)和一个 Web 应用程序，用于管理网络上的权威机构。 权限被分解为管理员身份和验证器节点。
管理员有权将协商一致参与委托给一组验证器节点。 管理员还可以将投票给网络内外的其他管理员。

![Governance DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **分散治理：** 网络权限的更改由选定的管理员通过链式投票进行管理。
* **验证器委派：** 权限可以管理在每个 PoA 部署中设置的验证器节点。
* **可审核更改历史记录：** 每个更改都记录在区块链上，提供透明度和可审计性。

### <a name="getting-started-with-governance"></a>Governance 入门

要通过治理 DApp 执行任何类型的事务，您需要使用以里瑟姆钱包。 最直接的方法是使用浏览器内钱包，如[MetaMask;](https://metamask.io)但是，由于这些智能合约部署在网络上，您还可以自动与治理协定进行交互。

安装 MetaMask 后，在浏览器中导航到 Governance DApp。  您可以在部署输出中通过 Azure 门户找到 URL。  如果您没有安装浏览器内钱包，您将无法执行任何操作;因此，您将无法执行任何操作。但是，您可以查看管理员状态。  

### <a name="becoming-an-admin"></a>成为管理员

如果您是部署在网络上的第一个成员，则会自动成为管理员，并且奇偶校验节点将列为验证程序。 如果您要加入网络，则需要以管理员身份获得多数（大于 50%） 的投票。现有管理集。 如果您选择不成为管理员，您的节点仍同步并验证区块链;但是，他们不参与块创建过程。 要开始投票过程以成为管理员，请选择 **"提名"** 并输入您的以子坊地址和别名。

![提名](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>候选人

选择"**候选"** 选项卡将显示当前一组候选管理员。  一旦候选人获得当前管理员的多数投票，该候选人将升任管理员。 要对候选人进行投票，请选择该行并选择 **"在 中投票**"。 如果您在投票中改变主意，请选择该候选人并选择 **"撤销"投票**。

![候选人](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>管理员

"**管理员"** 选项卡显示当前管理员集，并使您能够投反对票。  一旦管理员失去超过 50% 的支持，他们将作为网络上的管理员删除。 管理员拥有的任何验证器节点都会失去验证器状态，并成为网络上的事务节点。 管理员可能由于任何原因被移除;然而，由财团事先就政策达成一致。

![管理员](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>验证程序

选择**验证器**选项卡将显示实例的当前已部署奇偶校验节点及其当前状态（节点类型）。 每个联营成员在此列表中都有一组不同的验证器，因为此视图表示当前部署的联营成员。 如果实例是新部署的，但您尚未添加验证器，则可以选择**添加验证器**。 添加验证程序会自动选择区域平衡的奇偶校验节点集，并将其分配给验证器集。 如果部署的节点数超过允许的容量，则其余节点将成为网络上的事务节点。

每个验证程序的地址将通过 Azure 中的[标识存储区](#identity-store)自动分配。  如果节点出现故障，它将放弃其标识，从而允许部署中的另一个节点占据其位置。 此过程可确保您的共识参与获得高支持。

![验证程序](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>联盟名称

任何管理员可以更新联合体名称。  选择左上角的齿轮图标以更新联合体名称。

### <a name="account-menu"></a>帐户菜单

在右上角，是您的以非坊帐户别名和标识。  如果您是管理员，则可以更新别名。

![Account](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>以地庭开发<a id="tutorials"></a>

要编译、部署和测试智能合约，以下是您可以考虑的以里瑟姆开发的几个选项：
* [特鲁弗尔套房](https://www.trufflesuite.com/docs/truffle/overview)- 基于客户的以地庭开发环境
* [以非庭混音](https://remix-ide.readthedocs.io/en/latest/index.html )- 基于浏览器和本地以地开发环境

### <a name="compile-deploy-and-execute-smart-contract"></a>编译、部署和执行智能合同

在下面的示例中，您将创建一个简单的智能合约。 您可以使用 Truffle 编译智能合约并将其部署到区块链网络。 部署后，通过事务调用智能合约函数。

#### <a name="prerequisites"></a>先决条件

* 安装 [Python 2.7.15](https://www.python.org/downloads/release/python-2715/)。 特鲁弗尔和Web3需要Python。 选择安装选项以在路径中包含 Python。
* 安装特鲁夫拉夫 v5.0.5 `npm install -g truffle@v5.0.5`。 Truffle 要求安装多个工具，包括 [Node.js](https://nodejs.org)、[Git](https://git-scm.com/)。 有关详细信息，请参阅[特鲁弗尔文档](https://github.com/trufflesuite/truffle)。

### <a name="create-truffle-project"></a>创建 Truffle 项目

在编译和部署智能合约之前，需要创建 Truffle 项目。

1. 打开命令提示符或 shell。
1. 创建名为 `HelloWorld` 的文件夹。
1. 将目录更改为新`HelloWorld`文件夹。
1. 使用 命令`truffle init`初始化新的特鲁弗尔项目。

    ![创建新的特鲁弗尔项目](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>添加智能合约

在特鲁弗尔项目**的合同**子目录中创建智能合约。

1. 在 Truffle 项目`postBox.sol`**的合同**子目录中的命名中创建文件。
1. 将以下实体代码添加到**postBox.sol**。

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>使用特鲁弗尔部署智能合约

Truffle 项目包含一个用于区块链网络连接详细信息的配置文件。 修改配置文件以包括网络的连接信息。

> [!WARNING]
> 切勿通过网络发送以地庭私钥。 首先确保在本地对每个事务进行签名，然后通过网络发送已签名的事务。

1. 您需要[在部署区块链网络时使用的以地庭管理员帐户](#ethereum-settings)的助记符短语。 如果使用 MetaMask 创建帐户，则可以从 MetaMask 检索助记符。 选择 MetaMask 扩展右上角的管理员帐户图标，然后选择 **"设置>安全&隐私>显示种子词**"。
1. 将特鲁弗尔项目中`truffle-config.js`的内容替换为以下内容。 替换占位符终结点和助记符值。

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. 由于我们使用特鲁弗尔高清钱包提供商，请使用 命令`npm install truffle-hdwallet-provider --save`在您的项目中安装模块。

Truffle 使用迁移脚本将智能合约部署到区块链网络。 您需要一个迁移脚本来部署新的智能合约。

1. 添加新迁移以部署新合同。 在特鲁`2_deploy_contracts.js`弗尔项目的**迁移**子目录中创建文件。

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. 使用 Truffle 迁移命令部署到 PoA 网络。 在特鲁弗尔项目目录中的命令提示符下，运行：

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>调用智能合约功能

现在，智能合约已部署，您可以发送事务来调用函数。

1. 在 Truffle 项目目录中，创建名为`sendtransaction.js`的新文件。
1. 添加以下内容以**发送事务.js**。

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. 使用特鲁弗尔执行命令执行脚本。

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![通过事务执行脚本以调用函数](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>WebAssembly (WASM) 支持

新部署的 PoA 网络上已启用了 WebAssembly 支持。 它允许以任何转换为 Web-Assembly (Rust、C、C++) 的语言进行智能合同开发。 有关详细信息，请参阅：[奇偶校验技术](https://github.com/paritytech/pwasm-tutorial)Web 组装和教程的[奇偶校](https://wiki.parity.io/WebAssembly-Home)验

## <a name="faq"></a>FAQ

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>我注意到网络上有许多事务我没有发送。 这些事务来自哪里？

解锁[个人 API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html) 是不安全的操作。 机器人将侦听解锁的 Ethereum 帐户并尝试耗尽资金。 机器人假设这些帐户包含真实的以太，并试图第一个转走余额。 请勿在网络上启用个人 API。 而是使用 MetaMask 之类的钱包手动或以编程方式对交易进行预签名。

### <a name="how-to-ssh-onto-a-vm"></a>如何在 VM 上启用 SSH？

出于安全因素，未公开 SSH 端口。 请按照[本指南启用 SSH 端口](#ssh-access)。

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>如何设置审核成员或事务节点？

事务节点是一组奇偶校验客户端，与网络对等，但不参与共识。 这些节点仍可用于提交 Ethereum 事务并读取智能合同状态。 该机制适用于向网络上的非权威联合体成员提供可审计性。 为此，请按照["扩大联盟"](#growing-the-consortium)的步骤进行。

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>为什么 MetaMask 事务需要花费很长时间？

为了确保以正确的顺序接收事务，每个 Ethereum 事务都带有递增的 nonce。 如果您在不同网络上使用了 MetaMask 中的帐户，则需要重置 nonce 值。 单击设置图标（三栏）、设置、重置帐户。 事务历史记录随即清除，现在可以重新提交事务。

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>是否需要在 MetaMask 中指定燃料费用？

Ether 在权威证明联盟中没有用处。 因此，在 MetaMask 中提交交易时，无需指定天然气费用。

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>如果因无法预配 Azure OMS 而使我的部署失败，我该怎么办？

监视是一个可选功能。 在某些极少数情况下，部署由于无法成功预配 Azure 监视器资源而失败，则可以在没有 Azure 监视器的情况下重新部署。

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>公共 IP 部署是否与专用网络部署兼容？

不是。 对等互连需要双向通信，因此整个网络必须是公共的或私有的。

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>权威证明的预期事务吞吐量是多少？

事务吞吐量高度依赖于事务类型和网络拓扑。 使用简单的事务，我们已通过跨多个区域部署的网络，以平均每秒 400 个事务为基准进行评估。

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>如何订阅智能合同事件？

Ethereum 权威证明现在支持 Web 套接字。  检查部署输出以查找 Web 套接字 URL 和端口。

## <a name="next-steps"></a>后续步骤

有关更多 Azure 区块链解决方案，请参阅[Azure 区块链文档](https://docs.microsoft.com/azure/blockchain/)。
