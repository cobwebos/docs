---
title: "在 Azure Batch 池中配置节点终结点 | Microsoft Docs"
description: "如何在 Azure Batch 池中的计算节点上配置或禁用对 SSH 或 RDP 端口的访问。"
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: danlep
ms.openlocfilehash: fdc68744406c3e995a2764f93d4474b807337ff5
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>配置或禁用对 Azure Batch 池中计算节点的远程访问

默认情况下，Batch 允许已建立网络连接的[节点用户](/rest/api/batchservice/computenode/adduser)从外部连接到 Batch 池中的计算节点。 例如，用户可以在端口 3389 上通过远程桌面 (RDP) 连接到 Windows 池中的计算节点。 同样，在默认情况下，用户可以在端口 22 上通过安全外壳 (SSH) 连接到 Linux 池中的计算节点。 

在环境中，可能需要限制或禁用这些默认的外部访问设置。 使用 Batch API 设置 [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) 属性，可以修改这些设置。 

## <a name="about-the-pool-endpoint-configuration"></a>关于池终结点配置
终结点配置由一个或多个前端端口的[网络地址转换 (NAT) 池](/rest/api/batchservice/pool/add#inboundnatpool)构成。 （请不要将 NAT 池与计算节点的 Batch 池相混淆。）将每个 NAT 池设置为覆盖此池的计算节点上的默认连接设置。 

每个 NAT 池配置包括一个或多个[网络安全组 (NSG) 规则](/rest/api/batchservice/pool/add#networksecuritygrouprule)。 每个 NSG 规则允许或拒绝特定的网络流量流向终结点。 可以选择允许或拒绝所有流量、由[默认标记](../virtual-network/virtual-networks-nsg.md#default-tags)（例如“Internet”）标识的流量，或者来自特定 IP 地址或子网的流量。

### <a name="considerations"></a>注意事项
* 池终结点配置是池的[网络配置](/rest/api/batchservice/pool/add#NetworkConfiguration)的一部分。 网络配置可以选择性地包含用于将池加入 [Azure 虚拟网络](batch-virtual-network.md)的设置。 如果在虚拟网络中设置池，可以创建使用虚拟网络中的地址设置的 NSG 规则。
* 配置 NAT 池时，可以配置多个 NSG 规则。 将按优先顺序检查规则。 一旦应用某个规则，不再检查其他规则的匹配情况。


## <a name="example-deny-all-rdp-traffic"></a>示例：拒绝所有 RDP 流量

以下 C# 代码片段演示如何在 Windows 池中的计算节点上配置 RDP 终结点，用于拒绝所有网络流量。 该终结点使用 *60000 - 60099* 范围内的端口的前端池。 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>示例：拒绝来自 Internet 的所有 SSH 流量

以下 Python 代码片段演示如何在 Linux 池中的计算节点上配置 SSH 终结点，用于拒绝所有 Internet 流量。 该终结点使用 *4000 - 4100* 范围内的端口的前端池。 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                source_address_prefix='Internet'
                )
            ]
        )
        ]
    ) 
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>示例：允许来自特定 IP 地址的 RDP 流量

以下 C# 代码片段演示如何在 Windows 池中的计算节点上配置 RDP 终结点，以便仅允许来自 IP 地址 *198.51.100.7* 的 RDP 访问。 第二条 NSG 规则拒绝与该 IP 地址不匹配的流量。

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>示例：允许来自特定子网的 SSH 流量

以下 Python 代码片段演示如何在 Linux 池中的计算节点上配置 SSH 终结点，以便仅允许来自子网 *192.168.1.0/24* 的访问。 第二条 NSG 规则拒绝与该子网不匹配的流量。

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access='allow',
                source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                priority=175,
                access='deny',
                source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>后续步骤

- 有关 Azure 中 NSG 规则的详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/virtual-networks-nsg.md)。

- 有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。

