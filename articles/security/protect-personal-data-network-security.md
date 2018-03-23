---
title: 使用 Azure 网络安全功能保护个人数据 | Microsoft Docs
description: 使用 Azure 网络安全功能保护个人数据。 这可用于遵守一般数据保护条例 (GDPR)
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 54aeb22a501e248105931df341d23e524448155a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>使用网络安全功能保护个人数据：Azure 应用程序网关和网络安全组

本文提供可帮助你使用 Azure 应用程序网关和网络安全组保护个人数据的信息与过程。 此信息可能有助于遵守一般数据保护条例 (GDPR)。

用于保护个人数据隐私性的多分层安全策略的一个要素是防范 SQL 注入或跨站点脚本等常见漏洞攻击。 将不需要的网络流量隔离在 Azure 虚拟网络外部有助于防范潜在的敏感数据泄漏，此外，Microsoft Azure 提供了相应的工具来帮助防止数据受到攻击。

## <a name="scenario"></a>方案

总部位于美国的一家大型邮轮公司正在拓展其运营，以便在地中海、亚得里亚海和波罗的海以及英属岛屿提供路线。 为促进这些工作，该公司并购了意大利、德国、丹麦和英国的几家小型邮轮公司。

该公司使用 Microsoft Azure 在云中存储企业数据，并在虚拟机上运行用于处理和访问这些数据的应用程序。 这些数据包括个人身份信息，例如其全球客户群的姓名、地址、电话号码和信用卡信息。 另外，还包括传统的人力资源信息，例如，有关所有地点的公司员工的地址、电话号码、纳税识别号和其他信息。 该邮轮公司还维护一个奖励和忠诚计划会员的大型数据库，其中包含用于跟踪与当前和既往客户之间的关系的个人信息。

企业员工从公司的远程办公室访问网络，世界各地的旅行社有权访问某些公司资源，并使用 Azure VM 中托管的基于 Web 的应用程序来与资源交互。

## <a name="problem-statement"></a>问题陈述

该公司必须保护客户和员工个人数据的隐私，防止攻击者利用软件漏洞来运行恶意代码，从而可能会透露公司的基于云的应用程序存储或使用的个人数据。

## <a name="company-goal"></a>公司目标

公司的目标是确保未经授权的人员无法通过利用常见漏洞来访问企业 Azure 虚拟网络和其中的应用程序和数据。 

## <a name="solutions"></a>解决方案

Microsoft Azure 提供了安全机制来帮助防止不需要的流量进入 Azure 虚拟网络。 在传统上，入站和出站流量的控制是由防火墙执行的。 在 Azure 中，可以结合 Web 应用程序防火墙和网络安全组 (NSG) 使用应用程序网关（充当简单的分布式防火墙）。 使用这些工具可以检测和阻止不需要的网络流量。

### <a name="application-gatewayweb-application-firewall"></a>应用程序网关/Web 应用程序防火墙

[Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)的 [Web 应用程序防火墙](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF) 组件可保护 Web 应用程序，这些应用程序越来越多地成为利用常见已知漏洞展开的恶意攻击的目标。 集中式 WAF 可以抵御 Web 攻击，并简化安全管理，无需进行任何应用程序更改。

Azure WAF 可以应对各种攻击类别，包括 SQL 注入、跨站点脚本、HTTP 协议冲突和异常、机器人、爬网程序、扫描程序、常见应用程序配置错误、HTTP 拒绝服务和其他常见攻击，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含攻击。 

可以创建采用 WAF 的应用程序网关，或将 WAF 添加到现有的应用程序网关。 无论使用哪种方式，Azure 应用程序网关都需要自身的子网。

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>如何创建采用 WAF 的应用程序网关？ 

若要创建启用 WAF 的新应用程序网关，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“创建资源” > “网络” > “应用程序网关”。
5. 在显示的“基本信息”边栏选项卡中，输入以下字段的值：“名称”、“层”（“标准”或“WAF”）、“SKU 大小”（“小”、“中”或“大”）、“实例计数”（输入2 实现高可用性）、“订阅”、“资源组”和“位置”。
6. 在“虚拟网络”下的“设置”边栏选项卡中，单击“选择虚拟网络”。 执行此步骤后，会打开“选择虚拟网络”边栏选项卡。

7. 单击“新建”打开“创建虚拟网络”边栏选项卡。

8. 输入以下值：“名称”、“地址空间”、“子网名称”、“子网地址范围”。 单击“确定”。

9. 在“设置”边栏选项卡上的“前端 IP 配置”下选择“IP 地址类型”。

10. 依次单击“选择公共 IP 地址”、“新建”。

11. 接受默认值，单击“确定”。

12. 在“设置”边栏选项卡上的“侦听器配置”下，在“协议”下选择使用“HTTP”或“HTTPS”。 若要使用“HTTPS”，需要提供证书。

13. 配置 WAF 特定的设置：“防火墙状态”（“已启用”）和“防火墙模式”（“预防”）。 如果选择“检测”作为模式，则只会记录流量。

14. 查看“摘要”页，单击“确定”。 现在会让应用程序网关排队，并创建它。

创建应用程序网关后，可在门户中导航到该网关，并继续对其进行配置。

![创建的应用程序网关](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>如何将 WAF 添加到现有应用程序？

若要更新现有的应用程序网关以便在预防模式下支持 WAF，请执行以下操作：

1. 在 Azure 门户的“收藏夹”窗格中单击“所有资源”。

2. 在“所有资源”边栏选项卡中单击现有应用程序网关。 
>[!NOTE]
注意：如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入名称， 轻松访问 DNS 区域。
3. 单击“Web 应用程序防火墙”并更新应用程序网关设置：“升级到 WAF 层”（已选中）、“防火墙状态”（已启用）、“防火墙模式”（“预防”）。 还需要配置规则集，并配置已禁用的规则。

有关如何创建采用 WAF 的新应用程序网关，以及如何将 WAF 添加到现有应用程序网关的更多详细信息，请参阅[使用门户创建采用 Web 应用程序防火墙的应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)。

### <a name="network-security-groups"></a>网络安全组

[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) 包含一系列安全规则，这些规则可以允许或拒绝流向连接到 [Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/) (VNet) 的资源的网络流量。 NSG 可以关联到子网或单个 VM。 将 NSG 关联到子网时，规则适用于连接到该子网的所有资源。 也可通过将 NSG 关联到 VM 或 NIC 来进一步限制流量。

NSG 包含四个属性：名称、区域，资源组和规则。
>[!Note]
虽然 NSG 存在于一个资源组中，但可将其关联到任意资源组中的资源，只要该资源与 NSG 属于同一 Azure 区域。

NSG 规则包含九个属性：名称、协议（TCP、 UDP 或 \*，包括 ICMP 以及 UDP 和 TCP）、源端口范围、目标端口范围、源地址前缀、目标地址前缀、方向（入站或出站）、优先级（介于 100 和 4096 之间）和访问类型（允许或拒绝）。 所有 NSG 包含一组可以删除或者由创建的规则替代的默认规则。

#### <a name="how-do-i-implement-nsgs"></a>如何实施 NSG？

实施 NSG 需要进行规划，要考虑到几个设计注意事项。 这些事项包括每个订阅的 NSG 数量限制和每个 NSG 的规则数量限制、VNet 和子网设计、特殊规则、ICMP 流量、层与子网的隔离、负载均衡器、等等。

有关规划和实施 NSG 的更多指南和示例部署方案，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)。

#### <a name="how-do-i-create-rules-in-an-nsg"></a>如何在 NSG 中创建规则？

若要在现有 NSG 中创建入站规则，请执行以下操作：

1. 单击“所有服务”，并单击“网络安全组”。

2. 在 NSG 列表中，依次单击“NSG-FrontEnd”、“入站安全规则”。

3. 在“入站安全规则”列表中，单击“添加”。

4. 在以下字段中输入值：“名称”、“优先级”、“源”、“协议”、“源范围”、“目标”、“目标端口范围”和“操作”。

几秒钟后，新规则便会显示在 NSG 中。

![网络安全规则](media/protect-netsec/inbound-security.png)

有关如何在子网创建 NSG、创建规则以及将 NSG 与前端和后端子网相关联的详细说明，请参阅[使用 Azure 门户创建网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)。

## <a name="next-steps"></a>后续步骤

[Azure 网络安全](https://azure.microsoft.com/blog/azure-network-security/)

[Azure 网络安全最佳做法](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[获取有关网络安全组的信息](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[Web 应用程序防火墙 (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
