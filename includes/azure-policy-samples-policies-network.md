---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 6f0bc57bf73ae1bb44f022e399c941418ccafc03
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169882"
---
|名称 |说明 |效果 |版本 |
|---|---|---|---|
|[必须将自定义 IPsec/IKE 策略应用到所有 Azure 虚拟网络网关连接](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VPNGateways_CustomIpSecPolicies_Audit.json) |此策略可确保所有 Azure 虚拟网络网关连接均使用自定义 Internet 协议安全 (Ipsec)/Internet 密钥交换 (IKE) 策略。 支持的算法和密钥强度 - https://aka.ms/AA62kb0 |Audit、Disabled |1.0.0 |
|[应用服务应使用虚拟网络服务终结点](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |此策略审核任何未配置为使用虚拟网络服务终结点的应用服务。 |AuditIfNotExists、Disabled |1.0.0 |
|[Azure VPN 网关不应使用“基本”SKU](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VPNGateways_BasicSKU_Audit.json) |此策略可确保 VPN 网关不使用“基本”SKU。 |Audit、Disabled |1.0.0 |
|[Cosmos DB 应使用虚拟网络服务终结点](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |此策略审核任何未配置为使用虚拟网络服务终结点的 Cosmos DB。 |Audit、Disabled |1.0.0 |
|[创建虚拟网络时部署网络观察程序](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |此策略在具有虚拟网络的区域中创建网络观察程序资源。 需确保存在名为 networkWatcherRG 的资源组，该资源组用于部署网络观察程序实例。 |DeployIfNotExists |1.0.0 |
|[事件中心应使用虚拟网络服务终结点](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |此策略审核任何未配置为使用虚拟网络服务终结点的事件中心。 |AuditIfNotExists、Disabled |1.0.0 |
|[不应在网关子网中配置网络安全组](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |如果在网关子网中配置了网络安全组，则此策略会拒绝此配置。 将网络安全组分配到网关子网会导致网关停止运行。 |deny |1.0.0 |
|[Key Vault 应使用虚拟网络服务终结点](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |此策略审核任何未配置为使用虚拟网络服务终结点的 Key Vault。 |Audit、Disabled |1.0.0 |
|[网络接口应禁用 IP 转发](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkIPForwardingNic_Deny.json) |此策略拒绝启用了 IP 转发的网络接口。 IP 转发设置会禁止 Azure 在源和目标中检查网络接口。 网络安全团队应审查此设置。 |deny |1.0.0 |
|[网络接口不应使用公共 IP](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkPublicIPNic_Deny.json) |此策略拒绝配置了任何公共 IP 的网络接口。 公共 IP 地址允许 Internet 资源以入站方式与 Azure 资源通信，并允许 Azure 资源以出站方式与 Internet 通信。 网络安全团队应审查此设置。 |deny |1.0.0 |
|[应启用网络观察程序](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。 |auditIfNotExists |1.0.0 |
|[应阻止来自 Internet 的 RDP 访问](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |此策略审核任何允许来自 Internet 的 RDP 访问的网络安全规则 |Audit、Disabled |1.0.0 |
|[服务总线应使用虚拟网络服务终结点](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |此策略审核任何未配置为使用虚拟网络服务终结点的服务总线。 |AuditIfNotExists、Disabled |1.0.0 |
|[SQL Server 应使用虚拟网络服务终结点](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |此策略审核任何未配置为使用虚拟网络服务终结点的 SQL Server。 |AuditIfNotExists、Disabled |1.0.0 |
|[应阻止来自 Internet 的 SSH 访问](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |此策略审核任何允许来自 Internet 的 SSH 访问的网络安全规则 |Audit、Disabled |1.0.0 |
|[存储帐户应使用虚拟网络服务终结点](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |此策略审核任何未配置为使用虚拟网络服务终结点的存储帐户。 |Audit、Disabled |1.0.0 |
|[虚拟机应连接到已批准的虚拟网络](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |此策略审核任何已连接到未批准的虚拟网络的虚拟机。 |Audit、Deny、Disabled |1.0.0 |
|[虚拟网络应使用指定的虚拟网络网关](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |如果默认路由未指向指定的虚拟网络网关，则此策略会审核任何虚拟网络。 |AuditIfNotExists、Disabled |1.0.0 |
