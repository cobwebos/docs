---
title: Azure 安全基准的法规符合性详细信息
description: Azure 安全基准法规符合性内置计划的详细信息。 每个控制措施都映射到一个或多个协助评估的 Azure Policy 定义。
ms.date: 07/10/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 6cf12d1e63b2324b39683984eaab05321a29a126
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276730"
---
# <a name="details-of-the-azure-security-benchmark-regulatory-compliance-built-in-initiative"></a>Azure 安全基准法规符合性内置计划的详细信息

下文详细说明了 Azure Policy 法规符合性内置计划定义如何映射到 Azure 安全基准的符合性域和控制措施 。
有关此符合性标准的详细信息，请参阅 [Azure 安全基准](../../../security/benchmarks/overview.md)。 若要了解所有权，请参阅 [Azure Policy 策略定义](../concepts/definition-structure.md#type)和[云中责任分担](../../../security/fundamentals/shared-responsibility.md)。

以下映射是到 Azure 安全基准控制的映射。 使用右侧的导航栏可直接跳转到特定的符合性域。 许多控制措施都是使用 [Azure Policy](../overview.md) 计划定义实现的。 若要查看完整计划定义，请在 Azure 门户中打开“策略”，并选择“定义”页 。
然后，找到并选择 Azure 安全基准法规符合性内置计划定义。

此内置计划部署为 [Azure 安全基准蓝图示例](../../blueprints/samples/azure-security-benchmark.md)的一部分。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../overview.md) 定义关联。
> 这些策略可以帮助你[评估控件的符合性](../how-to/get-compliance-data.md)；但是，控件与一个或多个策略之间通常不是 1:1 或完全匹配。 因此，Azure Policy 中的符合性仅引用策略定义本身；这并不能确保你完全符合某个控制措施的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性标准的符合性域、控制措施和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/asb_audit.json)。

## <a name="network-security"></a>网络安全

### <a name="protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>在虚拟网络上使用网络安全组或 Azure 防火墙来保护资源

**ID**：Azure 安全基准 1.1 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在面向 Internet 的虚拟机上应用自适应网络强化建议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 安全中心会分析面向虚拟机的 Internet 的流量模式，并提供可减小潜在攻击面的网络安全组规则建议 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[所有 Internet 流量都应通过所部署的 Azure 防火墙进行路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure 安全中心已确认，你的某些子网未使用下一代防火墙进行保护。 通过使用 Azure 防火墙或受支持的下一代防火墙限制对子网的访问，保护子网免受潜在威胁的危害 |AuditIfNotExists、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[应用服务应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |此策略审核任何未配置为使用虚拟网络服务终结点的应用服务。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[应在 Kubernetes 服务上定义经授权的 IP 范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |通过仅向特定范围内的 IP 地址授予 API 访问权限，来限制对 Kubernetes 服务管理 API 的访问。 建议将访问权限限制给已获授权的 IP 范围，以确保只有受允许网络中的应用程序可以访问群集。 |Audit、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[容器注册表应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此策略审核任何未配置为使用虚拟网络服务终结点的容器注册表。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB 应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |此策略审核任何未配置为使用虚拟网络服务终结点的 Cosmos DB。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[事件中心应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |此策略审核任何未配置为使用虚拟网络服务终结点的事件中心。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[面向 Internet 的虚拟机应使用网络安全组进行保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范 VM 遭受潜在威胁。 如需详细了解如何使用 NSG 控制流量，请访问 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[应禁用虚拟机上的 IP 转发](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |在虚拟机的 NIC 上启用 IP 转发可让该计算机接收发往其他目标的流量。 极少需要启用 IP 转发（例如，将 VM 用作网络虚拟设备时），因此，此策略应由网络安全团队评审。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Key Vault 应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |此策略审核任何未配置为使用虚拟网络服务终结点的 Key Vault。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[应通过即时网络访问控制来保护虚拟机的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |建议通过 Azure 安全中心监视可能的网络适时 (JIT) 访问 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[应关闭虚拟机上的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |打开远程管理端口会使 VM 暴露在较高级别的 Internet 攻击风险之下。 此类攻击试图暴力破解凭据，来获取对计算机的管理员访问权限。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[应为 MariaDB 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |此策略审核未配置为使用专用终结点的 MariaDB 服务器。 有关更多详细信息，请访问 [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink)。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[应为 MySQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |此策略审核未配置为使用专用终结点的 MySQL 服务器。 有关更多详细信息，请访问 [https://aka.ms/mysqlprivatelink](https://aka.ms/mysqlprivatelink)。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[应为 PostgreSQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |此策略审核未配置为使用专用终结点的 PostgreSQL 服务器。 有关更多详细信息，请访问 [https://aka.ms/pgprivatelink](https://aka.ms/pgprivatelink)。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[服务总线应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |此策略审核任何未配置为使用虚拟网络服务终结点的服务总线。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server 应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |此策略审核任何未配置为使用虚拟网络服务终结点的 SQL Server。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[应限制对存储帐户的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |应限制对存储帐户的网络访问。 配置网络规则，以便只允许来自允许的网络的应用程序访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络或到公共 Internet IP 地址范围的流量授予访问权限 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[存储帐户应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |此策略审核任何未配置为使用虚拟网络服务终结点的存储帐户。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[子网应与网络安全组关联](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范子网遭受潜在威胁。 NSG 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝流向子网的网络流量。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[虚拟机应连接到已批准的虚拟网络](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |此策略审核任何已连接到未批准的虚拟网络的虚拟机。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[虚拟网络应使用指定的虚拟网络网关](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |如果默认路由未指向指定的虚拟网络网关，则此策略会审核任何虚拟网络。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |

### <a name="monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>监视和记录 VNet、子网和 NIC 的配置和流量

**ID**：Azure 安全基准 1.2 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用网络观察程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="protect-critical-web-applications"></a>保护关键的 Web 应用程序

**ID**：Azure 安全基准 1.3 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS 不应允许所有资源都能访问 API 应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |跨源资源共享 (CORS) 不应允许所有域都能访问你的 API 应用。 仅允许所需的域与 API 应用交互。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |
|[CORS 不应允许所有资源都能访问函数应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |跨源资源共享 (CORS) 不应允许所有域都能访问你的函数应用。 仅允许所需的域与函数应用交互。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |
|[CORS 不应允许所有资源都能访问你的 Web 应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |跨源资源共享 (CORS) 不应允许所有域都能访问你的 Web 应用程序。 仅允许所需的域与 Web 应用交互。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[确保 WEB 应用的“客户端证书(传入客户端证书)”设置为“打开”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |客户端证书允许应用请求传入请求的证书。 只有具有有效证书的客户端才能访问该应用。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[应为 API 应用禁用远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |远程调试需要在 API 应用上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[应对函数应用禁用远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |远程调试需要在函数应用上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[应禁用 Web 应用程序的远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |远程调试需要在 Web 应用程序上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="deny-communications-with-known-malicious-ip-addresses"></a>拒绝与已知的恶意 IP 地址通信

**ID**：Azure 安全基准 1.4 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在面向 Internet 的虚拟机上应用自适应网络强化建议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 安全中心会分析面向虚拟机的 Internet 的流量模式，并提供可减小潜在攻击面的网络安全组规则建议 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[所有 Internet 流量都应通过所部署的 Azure 防火墙进行路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure 安全中心已确认，你的某些子网未使用下一代防火墙进行保护。 通过使用 Azure 防火墙或受支持的下一代防火墙限制对子网的访问，保护子网免受潜在威胁的危害 |AuditIfNotExists、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[应启用 Azure DDoS 防护标准](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |应为属于应用程序网关且具有公共 IP 子网的所有虚拟网络启用 DDoS 保护标准。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[应通过即时网络访问控制来保护虚拟机的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |建议通过 Azure 安全中心监视可能的网络适时 (JIT) 访问 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="record-network-packets-and-flow-logs"></a>记录网络数据包和流日志

**ID**：Azure 安全基准 1.5 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用网络观察程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>使用自动化工具监视网络资源配置并检测更改

**ID**：Azure 安全基准 1.11 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[部署必备组件用于审核“管理模板 - 网络”中的 Windows VM 配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F985285b7-b97a-419c-8d48-c88cc934c8d8) |此策略创建一个 Guest Configuration 分配用于审核以下组策略类别中采用不合规设置的 Windows 虚拟机：“管理模板 - 网络”。 它还会创建系统分配的托管标识，并部署 Guest Configuration 的 VM 扩展。 此策略应结合计划中的相应审核策略一起使用。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministrativeTemplatesNetwork_Deploy.json) |
|[部署必备组件用于审核“安全选项 - Microsoft 网络服务器”中的 Windows VM 配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86880e5c-df35-43c5-95ad-7e120635775e) |此策略创建一个 Guest Configuration 分配用于审核以下组策略类别中采用不合规设置的 Windows 虚拟机：“安全选项 - Microsoft 网络服务器”。 它还会创建系统分配的托管标识，并部署 Guest Configuration 的 VM 扩展。 此策略应结合计划中的相应审核策略一起使用。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_Deploy.json) |
|[部署必备组件用于审核“安全选项 - 网络访问”中的 Windows VM 配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56a3ab2-89d1-44de-ac0d-2ada5962e22a) |此策略创建一个 Guest Configuration 分配用于审核以下组策略类别中采用不合规设置的 Windows 虚拟机：“安全选项 - 网络访问”。 它还会创建系统分配的托管标识，并部署 Guest Configuration 的 VM 扩展。 此策略应结合计划中的相应审核策略一起使用。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_Deploy.json) |
|[部署必备组件用于审核“安全选项 - 网络安全性”中的 Windows VM 配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36e17963-7202-494a-80c3-f508211c826b) |此策略创建一个 Guest Configuration 分配用于审核以下组策略类别中采用不合规设置的 Windows 虚拟机：“安全选项 - 网络安全性”。 它还会创建系统分配的托管标识，并部署 Guest Configuration 的 VM 扩展。 此策略应结合计划中的相应审核策略一起使用。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkSecurity_Deploy.json) |
|[显示“管理模板 - 网络”中 Windows VM 配置的审核结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7229bd6a-693d-478a-87f0-1dc1af06f3b8) |此策略应结合计划中的相应部署策略一起使用。 此定义允许 Azure Policy 处理以下组策略类别中采用不合规设置的 Windows 虚拟机的审核结果：“管理模板 - 网络”。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministrativeTemplatesNetwork_Audit.json) |
|[显示“安全选项 - Microsoft 网络客户端”中 Windows VM 配置的审核结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffcbc55c9-f25a-4e55-a6cb-33acb3be778b) |此策略应结合计划中的相应部署策略一起使用。 此定义允许 Azure Policy 处理以下组策略类别中采用不合规设置的 Windows 虚拟机的审核结果：“安全选项 - Microsoft 网络客户端”。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkClient_Audit.json) |
|[显示“安全选项 - Microsoft 网络服务器”中 Windows VM 配置的审核结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fe4ef56-7576-4dc4-8e9c-26bad4b087ce) |此策略应结合计划中的相应部署策略一起使用。 此定义允许 Azure Policy 处理以下组策略类别中采用不合规设置的 Windows 虚拟机的审核结果：“安全选项 - Microsoft 网络服务器”。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_Audit.json) |
|[显示“安全选项 - 网络安全性”中 Windows VM 配置的审核结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c028d2a-1889-45f6-b821-31f42711ced8) |此策略应结合计划中的相应部署策略一起使用。 此定义允许 Azure Policy 处理以下组策略类别中采用不合规设置的 Windows 虚拟机的审核结果：“安全选项 - 网络安全性”。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkSecurity_Audit.json) |

## <a name="logging-and-monitoring"></a>日志记录和监视

### <a name="configure-central-security-log-management"></a>配置安全日志集中管理

**ID**：Azure 安全基准 2.2 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应该对订阅启用 Log Analytics 监视代理的自动预配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |启用 Log Analytics 监视代理的自动预配，以便收集安全数据 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Monitor 日志配置文件应收集“写入”、“删除”和“操作”类别的日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |此策略可确保日志配置文件收集类别为 "write"、"delete" 和 "action" 的日志 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[Azure Monitor 应从所有区域收集活动日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |此策略审核不从所有 Azure 支持区域（包括全局）导出活动的 Azure Monitor 日志配置文件。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
|[部署必备组件用于审核其上 Log Analytics 代理未按预期连接的 Windows VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68511db2-bd02-41c4-ae6b-1900a012968a) |此策略创建一个 Guest Configuration 分配用于审核其上 Log Analytics 代理未连接到指定工作区的 Windows 虚拟机。 它还会创建系统分配的托管标识，并部署 Guest Configuration 的 VM 扩展。 此策略应结合计划中的相应审核策略一起使用。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_Deploy.json) |
|[显示其上 Log Analytics 代理未按预期连接的 Windows VM 的审核结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa030a57e-4639-4e8f-ade9-a92f33afe7ee) |此策略应结合计划中的相应部署策略一起使用。 此定义允许 Azure Policy 处理其上 Log Analytics 代理未连接到指定工作区的 Windows 虚拟机的审核结果。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_Audit.json) |
|[应在虚拟机规模集上安装 Log Analytics 代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |此策略审核是否有任何 Windows/Linux 虚拟机规模集未安装 Log Analytics 代理。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[应在虚拟机上安装 Log Analytics 代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |此策略审核是否有任何 Windows/Linux 虚拟机未安装 Log Analytics 代理。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="enable-audit-logging-for-azure-resources"></a>为 Azure 资源启用审核日志记录

**ID**：Azure 安全基准 2.3 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[审核诊断设置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |审核所选资源类型的诊断设置。 |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[应启用 SQL 服务器上的审核](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |应在 SQL 服务器上启用审核以跟踪服务器上所有数据库的数据库活动，并将其保存在审核日志中。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[应启用应用程序服务中的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |审核确认已在应用上启用诊断日志。 如果发生安全事件或网络遭泄露，这样便可以重新创建活动线索用于调查目的。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[应启用 Azure Data Lake Store 的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[应启用 Azure 流分析的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[应启用 Batch 帐户的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[应启用 Data Lake Analytics 的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[应启用事件中心的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[应启用 IoT 中心的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[应启用 Key Vault 的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |审核是否已启用诊断日志。 使用此策略可在发生安全事件或网络受到安全威胁时重新创建用于调查的活动线索 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[应启用逻辑应用的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[应启用搜索服务的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[应启用服务总线的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[应当启用虚拟机规模集中的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |建议启用日志，以便在出现某个事件或遭到入侵后需要进行调查时可以重新创建活动线索。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[SQL 审核设置中应包含配置为捕获关键活动的操作组](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |AuditActionsAndGroups 属性应至少包含 SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP FAILED_DATABASE_AUTHENTICATION_GROUP、BATCH_COMPLETED_GROUP 以确保全面审核日志记录 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="collect-security-logs-from-operating-systems"></a>从操作系统收集安全日志

**ID**：Azure 安全基准 2.4 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应该对订阅启用 Log Analytics 监视代理的自动预配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |启用 Log Analytics 监视代理的自动预配，以便收集安全数据 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[部署必备组件用于审核其上 Log Analytics 代理未按预期连接的 Windows VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68511db2-bd02-41c4-ae6b-1900a012968a) |此策略创建一个 Guest Configuration 分配用于审核其上 Log Analytics 代理未连接到指定工作区的 Windows 虚拟机。 它还会创建系统分配的托管标识，并部署 Guest Configuration 的 VM 扩展。 此策略应结合计划中的相应审核策略一起使用。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_Deploy.json) |
|[显示其上 Log Analytics 代理未按预期连接的 Windows VM 的审核结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa030a57e-4639-4e8f-ade9-a92f33afe7ee) |此策略应结合计划中的相应部署策略一起使用。 此定义允许 Azure Policy 处理其上 Log Analytics 代理未连接到指定工作区的 Windows 虚拟机的审核结果。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_Audit.json) |
|[应在虚拟机规模集上安装 Log Analytics 代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |此策略审核是否有任何 Windows/Linux 虚拟机规模集未安装 Log Analytics 代理。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[应在虚拟机上安装 Log Analytics 代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |此策略审核是否有任何 Windows/Linux 虚拟机未安装 Log Analytics 代理。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="configure-security-log-storage-retention"></a>配置安全日志存储保留期

**ID**：Azure 安全基准 2.5 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应将 SQL 服务器的审核保留期配置为大于 90 天](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |审核配置的审核保持期少于 90 天的 SQL 服务器。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="enable-alerts-for-anomalous-activity"></a>启用针对异常活动的警报

**ID**：Azure 安全基准 2.7 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在 SQL 托管实例上启用高级数据安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应在 SQL 服务器上启用高级数据安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="centralize-anti-malware-logging"></a>集中进行反恶意软件日志记录

**ID**：Azure 安全基准 2.8 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在虚拟机规模集上安装终结点保护解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |审核终结点保护解决方案在虚拟机规模集上的存在性和运行状况 ，以保护其免受威胁和漏洞的侵害。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Antimalware for Azure 应配置为自动更新保护签名](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |此策略会审核所有未配置自动更新 Microsoft Antimalware 保护签名的 Windows 虚拟机。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[监视 Azure 安全中心 Endpoint Protection 的缺失情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |建议通过 Azure 安全中心监视未安装 Endpoint Protection 代理的服务器 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="identity-and-access-control"></a>标识和访问控制

### <a name="maintain-an-inventory-of-administrative-accounts"></a>维护管理帐户的清单

**ID**：Azure 安全基准 3.1 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只多只为订阅指定 3 个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建议最多指定 3 个订阅所有者，以减少可能出现的已遭入侵的所有者做出的违规行为。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[应从订阅中删除拥有所有者权限的已弃用帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |应从订阅中删除拥有所有者权限的已弃用帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[应从订阅中删除拥有所有者权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |为了防止发生未受监视的访问，应从订阅中删除拥有所有者权限的外部帐户。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[应为订阅分配了多个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |建议指定多个订阅所有者，这样才会有管理员访问冗余。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="use-dedicated-administrative-accounts"></a>使用专用管理帐户

**ID**：Azure 安全基准 3.3 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只多只为订阅指定 3 个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建议最多指定 3 个订阅所有者，以减少可能出现的已遭入侵的所有者做出的违规行为。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[部署必备组件来审核其“管理员”组包含任意指定成员的 Windows VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F144f1397-32f9-4598-8c88-118decc3ccba) |此策略创建一个 Guest Configuration 分配用于审核其“管理员”组包含任意指定成员的 Windows 虚拟机。 它还会创建系统分配的托管标识，并部署 Guest Configuration 的 VM 扩展。 此策略应结合计划中的相应审核策略一起使用。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_Deploy.json) |
|[部署必备组件以审核其“管理员”组不包含所有指定成员的 Windows VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93507a81-10a4-4af0-9ee2-34cf25a96e98) |此策略创建一个 Guest Configuration 分配用于审核其“管理员”组不包含所有指定成员的 Windows 虚拟机。 它还会创建系统分配的托管标识，并部署 Guest Configuration 的 VM 扩展。 此策略应结合计划中的相应审核策略一起使用。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_Deploy.json) |
|[部署必备组件来审核“管理员”组不只包含指定成员的 Windows VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb821191b-3a12-44bc-9c38-212138a29ff3) |此策略创建一个 Guest Configuration 分配用于审核其“管理员”组不只包含指定成员的 Windows 虚拟机。 它还会创建系统分配的托管标识，并部署 Guest Configuration 的 VM 扩展。 此策略应结合计划中的相应审核策略一起使用。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |deployIfNotExists |[1.2.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_Deploy.json) |
|[显示其“管理员”组包含任意指定成员的 Windows VM 的审核结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbde62c94-ccca-4821-a815-92c1d31a76de) |此策略应结合计划中的相应部署策略一起使用。 此定义允许 Azure Policy 处理其“管理员”组包含任意指定成员的 Windows 虚拟机的审核结果。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_Audit.json) |
|[显示其“管理员”组不包含所有指定成员的 Windows VM 的审核结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3b44e5d-1456-475f-9c67-c66c4618e85a) |此策略应结合计划中的相应部署策略一起使用。 此定义允许 Azure Policy 处理其“管理员”组不包含所有指定成员的 Windows 虚拟机的审核结果。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_Audit.json) |
|[显示其“管理员”组不只包含指定成员的 Windows VM 的审核结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc7cda28-f867-4311-8497-a526129a8d19) |此策略应结合计划中的相应部署策略一起使用。 此定义允许 Azure Policy 处理其“管理员”组不只包含指定成员的 Windows 虚拟机的审核结果。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_Audit.json) |
|[应为订阅分配了多个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |建议指定多个订阅所有者，这样才会有管理员访问冗余。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>对所有基于 Azure Active Directory 的访问使用多重身份验证

**ID**：Azure 安全基准 3.5 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应对订阅中拥有写入权限的帐户启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |为了防止帐户或资源出现违规问题，应为所有拥有写入特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[应在对订阅拥有所有者权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |为了防止帐户或资源出现违规问题，应为所有拥有所有者权限的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[应在对订阅拥有读取权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |为了防止帐户或资源出现违规问题，应为所有拥有读取特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="use-azure-active-directory"></a>使用 Azure Active Directory

**ID**：Azure 安全基准 3.9 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应该为 SQL 服务器预配 Azure Active Directory 管理员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |审核确认已为 SQL Server 预配了 Azure Active Directory 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[确保在 API 应用中启用“注册到 Azure Active Directory”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86d97760-d216-4d81-a3ad-163087b2b6c3) |应用服务中的托管服务标识可以让应用更安全，因为不需在应用中存储机密，例如连接字符串中的凭据。 在应用服务中注册到 Azure Active Directory 时，应用将安全连接到其他 Azure 服务，而无需输入用户名和密码 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_AD_Enabled.json) |
|[确保在函数应用中启用“注册到 Azure Active Directory”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0473e7a-a1ba-4e86-afb2-e829e11b01d8) |应用服务中的托管服务标识可以让应用更安全，因为不需在应用中存储机密，例如连接字符串中的凭据。 在应用服务中注册到 Azure Active Directory 时，应用将安全连接到其他 Azure 服务，而无需输入用户名和密码 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_AD_Enabled.json) |
|[确保在 WEB 应用中启用“注册到 Azure Active Directory”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa81768c-cb87-4ce2-bfaa-00baa10d760c) |应用服务中的托管服务标识可以让应用更安全，因为不需在应用中存储机密，例如连接字符串中的凭据。 在应用服务中注册到 Azure Active Directory 时，应用将安全连接到其他 Azure 服务，而无需输入用户名和密码 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_AD_Enabled.json) |
|[Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |审核 Service Fabric 中仅通过 Azure Active Directory 进行客户端身份验证 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="regularly-review-and-reconcile-user-access"></a>定期评审和协调用户访问权限

**ID**：Azure 安全基准 3.10 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应从订阅中删除弃用的帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |应从订阅中删除弃用的帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[应从订阅中删除拥有所有者权限的已弃用帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |应从订阅中删除拥有所有者权限的已弃用帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[应从订阅中删除拥有所有者权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |为了防止发生未受监视的访问，应从订阅中删除拥有所有者权限的外部帐户。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[应从订阅中删除拥有读取权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |应从订阅中删除拥有读取特权的外部帐户，以防发生未受监视的访问。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[应从订阅中删除具有写入权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |应从订阅中删除拥有写入特权的外部帐户，以防发生未受监视的访问。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

## <a name="data-protection"></a>数据保护

### <a name="maintain-an-inventory-of-sensitive-information"></a>维护敏感信息清单

**ID**：Azure 安全基准 4.1 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应对 SQL 数据库中的敏感数据进行分类](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Azure 安全中心监视 SQL 数据库的数据发现和分类扫描结果，并建议将数据库中的敏感数据分类以改善监视效果并提升安全性 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |

### <a name="encrypt-all-sensitive-information-in-transit"></a>加密传输中的所有敏感信息

**ID**：Azure 安全基准 4.4 所有权：共享

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只能通过 HTTPS 访问 API 应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[应为 MySQL 数据库服务器启用“强制 SSL 连接”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |此策略审核不强制 SSL 连接的任何 MySQL 服务器。 Azure Database for MySQL 支持使用安全套接字层 (SSL) 将 Azure Database for MySQL 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |此策略审核不强制 SSL 连接的任何 PostgreSQL 服务器。 Azure Database for PostgreSQL 倾向于使用安全套接字层 (SSL) 将客户端应用程序连接到 PostgreSQL 服务。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[应仅在 API 应用中需要 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |启用 FTPS 强制以实现增强的安全性 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[应仅在函数应用中要求使用 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |启用 FTPS 强制以实现增强的安全性 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[应仅在 Web 应用中要求使用 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |启用 FTPS 强制以实现增强的安全性 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |
|[应该只能通过 HTTPS 访问函数应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[应在 API 应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[应在函数应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[应在 Web 应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[只能与 Azure Cache for Redis 建立安全连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |审核是否仅启用通过 SSL 来与 Azure Redis 缓存建立连接。 使用安全连接可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听攻击和会话劫持等网络层攻击 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[应启用安全传输到存储帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |审核存储帐户中安全传输的要求。 安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听和会话劫持等网络层攻击 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[只能通过 HTTPS 访问 Web 应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="use-an-active-discovery-tool-to-identify-sensitive-data"></a>使用有效的发现工具识别敏感数据

**ID**：Azure 安全基准 4.5 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在 SQL 托管实例上启用高级数据安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应在 SQL 服务器上启用高级数据安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应对 SQL 数据库中的敏感数据进行分类](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Azure 安全中心监视 SQL 数据库的数据发现和分类扫描结果，并建议将数据库中的敏感数据分类以改善监视效果并提升安全性 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |

### <a name="use-azure-rbac-to-control-access-to-resources"></a>使用 Azure RBAC 控制对资源的访问

**ID**：Azure 安全基准 4.6 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[审核自定义 RBAC 规则的使用情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |审核“所有者、参与者、读者”等内置角色而不是容易出错的自定义 RBAC 角色。 使用自定义角色被视为例外，需要进行严格的审查和威胁建模 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |若要对用户可以执行的操作提供粒度筛选，请使用基于角色的访问控制 (RBAC) 来管理 Kubernetes 服务群集中的权限并配置相关授权策略。 |Audit、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="encrypt-sensitive-information-at-rest"></a>加密静态的敏感信息

**ID**：Azure 安全基准 4.8 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[自动化帐户变量应加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |存储敏感数据时，请务必启用自动化帐户变量资产加密 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[应在虚拟机上应用磁盘加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |建议通过 Azure 安全中心监视未启用磁盘加密的 VM |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric 使用主要群集证书为节点之间的通信提供三个保护级别（None、Sign 和 EncryptAndSign）。 设置保护级别以确保所有节点到节点消息均已进行加密和数字签名 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[应使用自己的密钥加密 SQL 托管实例的 TDE 保护程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |使用你自己的密钥支持的透明数据加密(TDE)增加了透明度和对 TDE 保护器的控制，增强了由 HSM 提供支持的外部服务的安全性，并促进了职责划分。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[应使用自己的密钥加密 SQL 服务器的 TDE 保护器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |使用你自己的密钥支持的透明数据加密(TDE)增加了透明度和对 TDE 保护器的控制，增强了由 HSM 提供支持的外部服务的安全性，并促进了职责划分。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[应在 SQL 数据库上启用透明数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |应启用透明数据加密以保护静态数据并满足符合性要求 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[应当加密未附加的磁盘](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |此策略会审核未启用加密的所有未附加磁盘。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="log-and-alert-on-changes-to-critical-azure-resources"></a>记录对关键 Azure 资源的更改并发出警报

**ID**：Azure 安全基准 4.9 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor 应从所有区域收集活动日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |此策略审核不从所有 Azure 支持区域（包括全局）导出活动的 Azure Monitor 日志配置文件。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

## <a name="vulnerability-management"></a>漏洞管理

### <a name="run-automated-vulnerability-scanning-tools"></a>运行自动化漏洞扫描工具

**ID**：Azure 安全基准 5.1 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在 SQL 托管实例上启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |审核未启用定期漏洞评估扫描的每个 SQL 托管实例。 漏洞评估可发现、跟踪和帮助你修正潜在数据库漏洞。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[应对虚拟机启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |监视 Azure 安全中心漏洞评估在虚拟机上检测到的漏洞。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[应对 SQL 服务器启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |审核未启用定期漏洞评估扫描的 Azure SQL 服务器。 漏洞评估可发现、跟踪和帮助你修正潜在数据库漏洞。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="deploy-automated-operating-system-patch-management-solution"></a>部署操作系统修补程序自动化管理解决方案

**ID**：Azure 安全基准 5.2 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[确保用作 API 应用一部分的“.NET Framework”版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc2e7ca55-f62c-49b2-89a4-d41eb661d2f0) |我们定期发布适用于 .NET Framework 软件的更新版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 .NET Framework 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_NETFrameWork_Latest.json) |
|[确保用作函数应用一部分的“.NET Framework”版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10c1859c-e1a7-4df3-ab97-a487fa8059f6) |我们定期发布适用于 .NET Framework 软件的更新版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 .NET Framework 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_NETFrameWork_Latest.json) |
|[确保用作 Web 应用一部分的“.NET Framework”版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F843664e0-7563-41ee-a9cb-7522c382d2c4) |我们定期发布适用于 .NET Framework 软件的更新版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 .NET Framework 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_NETFrameWork_Latest.json) |
|[应在虚拟机规模集上安装系统更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |审核是否缺少系统安全更新和关键更新，为了确保 Windows 和 Linux 虚拟机规模集的安全，应安装这些更新。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[应在计算机上安装系统更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |建议通过 Azure 安全中心监视服务器上缺失的安全系统更新 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="deploy-automated-third-party-software-patch-management-solution"></a>部署第三方软件修补程序自动化管理解决方案

**ID**：Azure 安全基准 5.3 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[确保用作 API 应用一部分的“Java 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |我们定期发布适用于 Java 的更高版本来解决安全漏洞或包含更多功能。 建议使用 API 应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[确保用作函数应用一部分的“Java 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |我们定期发布适用于 Java 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用函数应用的最新 Java 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[确保用作 Web 应用一部分的“Java 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |我们定期发布适用于 Java 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 Java 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |
|[确保用作 API 应用一部分的“PHP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |我们定期发布适用于 PHP 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 API 应用的最新 PHP 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[确保用作函数应用一部分的“PHP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab965db2-d2bf-4b64-8b39-c38ec8179461) |我们定期发布适用于 PHP 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用函数应用的最新 PHP 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_PHP_Latest.json) |
|[确保用作 WEB 应用一部分的“PHP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |我们定期发布适用于 PHP 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 PHP 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |
|[确保用作 API 应用一部分的“Python 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |我们定期发布适用于 Python 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 API 应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[确保用作函数应用一部分的“Python 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |我们定期发布适用于 Python 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用函数应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[确保用作 Web 应用一部分的“Python 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |我们定期发布适用于 Python 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |
|[Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |将 Kubernetes 服务群集升级到更高 Kubernetes 版本，以抵御当前 Kubernetes 版本中的已知漏洞。 Kubernetes 版本 1.11.9+、1.12.7+、1.13.5+ 和 1.14.0+ 中已修补漏洞 CVE-2019-9946 |Audit、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

### <a name="use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>使用风险评分流程确定所发现漏洞的修正优先级

**ID**：Azure 安全基准 5.5 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应修正容器安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在安装了 Docker 的计算机上审核安全配置中的漏洞，并在 Azure 安全中心显示为建议。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[应修复计算机上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |建议通过 Azure 安全中心监视不满足配置的基线的服务器 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[应修复虚拟机规模集上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[应修复 SQL 数据库中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |监视漏洞评估扫描结果并提供如何补救数据库漏洞的相关建议。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[应通过漏洞评估解决方案修复漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |建议在 Azure 安全中心监视漏洞评估解决方案检测到的漏洞和没有漏洞评估解决方案的 VM。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

## <a name="inventory-and-asset-management"></a>清单和资产管理

### <a name="use-only-approved-applications"></a>只使用已批准的应用程序

**ID**：Azure 安全基准 6.8 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在计算机中启用自适应应用程序控制以定义安全应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |启用应用程序控制，以定义计算机中正在运行的已知安全应用程序列表，并在其他应用程序运行时向你发出警报。 这有助于强化计算机免受恶意软件的侵害。 为了简化配置和维护规则的过程，安全中心使用机器学习来分析在每台计算机上运行的应用程序，并建议已知安全应用程序的列表。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="use-only-approved-azure-services"></a>只使用已批准的 Azure 服务

**ID**：Azure 安全基准 6.9 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[存储帐户应迁移到新的 Azure 资源管理器资源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37e0d2fe-28a5-43d6-a273-67d37d1f5606) |使用新的 Azure 资源管理器为存储帐户提供安全增强功能，例如：更强大的访问控制 (RBAC)、更好的审核、基于 Azure 资源管理器的部署和监管、对托管标识的访问权限、访问密钥保管库以获取机密、基于 Azure AD 的身份验证以及对标记和资源组的支持，以简化安全管理 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |
|[应将虚拟机迁移到新的 Azure 资源管理器资源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |对虚拟机使用新的 Azure 资源管理器以提供安全增强功能，例如：更强的访问控制 (RBAC)、更佳审核功能、基于 Azure 资源管理器的部署和治理、对托管标识的访问、访问密钥保管库以获取机密、基于 Azure AD 的身份验证以及支持使用标记和资源组简化安全管理 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="implement-approved-application-list"></a>实现已批准的应用程序列表

**ID**：Azure 安全基准 6.10 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在计算机中启用自适应应用程序控制以定义安全应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |启用应用程序控制，以定义计算机中正在运行的已知安全应用程序列表，并在其他应用程序运行时向你发出警报。 这有助于强化计算机免受恶意软件的侵害。 为了简化配置和维护规则的过程，安全中心使用机器学习来分析在每台计算机上运行的应用程序，并建议已知安全应用程序的列表。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="secure-configuration"></a>安全配置

### <a name="maintain-secure-azure-resource-configurations"></a>维护安全的 Azure 资源配置

**ID**：Azure 安全基准 7.3 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[预览版\]:应在 Kubernetes 服务上定义 Pod 安全策略](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3abeb944-26af-43ee-b83d-32aaf060fb94) |通过删除不必要的应用程序特权，来定义 Pod 安全策略以减少攻击途径。 建议将 Pod 安全策略配置为仅允许 Pod 访问它们有权访问的资源。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnablePSP_KubernetesService_Audit.json) |

### <a name="maintain-secure-operating-system-configurations"></a>维护安全的操作系统配置

**ID**：Azure 安全基准 7.4 所有权：共享

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应修正容器安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在安装了 Docker 的计算机上审核安全配置中的漏洞，并在 Azure 安全中心显示为建议。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[应修复计算机上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |建议通过 Azure 安全中心监视不满足配置的基线的服务器 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[应修复虚拟机规模集上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="implement-automated-configuration-monitoring-for-azure-services"></a>针对 Azure 服务实现自动化配置监视

**ID**：Azure 安全基准 7.9 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[预览版\]:应在 Kubernetes 服务上定义 Pod 安全策略](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3abeb944-26af-43ee-b83d-32aaf060fb94) |通过删除不必要的应用程序特权，来定义 Pod 安全策略以减少攻击途径。 建议将 Pod 安全策略配置为仅允许 Pod 访问它们有权访问的资源。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnablePSP_KubernetesService_Audit.json) |

### <a name="implement-automated-configuration-monitoring-for-operating-systems"></a>针对操作系统实现自动化配置监视

**ID**：Azure 安全基准 7.10 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应修正容器安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在安装了 Docker 的计算机上审核安全配置中的漏洞，并在 Azure 安全中心显示为建议。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[应修复计算机上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |建议通过 Azure 安全中心监视不满足配置的基线的服务器 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[应修复虚拟机规模集上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="manage-azure-secrets-securely"></a>安全地管理 Azure 机密

**ID**：Azure 安全基准 7.11 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[密钥保管库对象应可恢复](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |此策略审核密钥保管库对象是否不可恢复。 软删除功能有助于在给定的保留期（90 天）内有效地保留资源，即使在 DELETE 操作之后也是如此，同时提供对象已被删除的外观。 启用“清除保护”后，在长达 90 天的保留期到期之前，不能清除处于已删除状态的保管库或对象。 这些保管库和对象仍然可以恢复，从而向客户保证将遵循保留策略。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="manage-identities-securely-and-automatically"></a>安全且自动地管理标识 

**ID**：Azure 安全基准 7.12 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在 API 应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[应在函数应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[应在 Web 应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

## <a name="malware-defense"></a>恶意软件防护

### <a name="use-centrally-managed-anti-malware-software"></a>使用集中管理的反恶意软件

**ID**：Azure 安全基准 8.1 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在虚拟机规模集上安装终结点保护解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |审核终结点保护解决方案在虚拟机规模集上的存在性和运行状况 ，以保护其免受威胁和漏洞的侵害。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[监视 Azure 安全中心 Endpoint Protection 的缺失情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |建议通过 Azure 安全中心监视未安装 Endpoint Protection 代理的服务器 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-anti-malware-software-and-signatures-are-updated"></a>确保反恶意软件和签名已更新

**ID**：Azure 安全基准 8.3 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Antimalware for Azure 应配置为自动更新保护签名](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |此策略会审核所有未配置自动更新 Microsoft Antimalware 保护签名的 Windows 虚拟机。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |

## <a name="data-recovery"></a>数据恢复

### <a name="ensure-regular-automated-back-ups"></a>确保定期执行自动备份

**ID**：Azure 安全基准 9.1 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为虚拟机启用 Azure 备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |此策略可帮助审核是否为所有虚拟机启用了 Azure 备份服务。 Azure 备份是一个经济高效的一键式备份解决方案，可简化数据恢复，并且比其他云备份服务更易于启用。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[应为 Azure Database for MariaDB 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |此策略将审核未启用异地冗余备份的任何 Azure Database for MariaDB。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[应为 Azure Database for MySQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |此策略将审核未启用异地冗余备份的任何 Azure Database for MySQL。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[应为 Azure Database for PostgreSQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |此策略将审核未启用异地冗余备份的任何 Azure Database for PostgreSQL。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[应为 Azure SQL 数据库启用长期异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |此策略将审核未启用长期异地冗余备份的任何 Azure SQL 数据库。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>执行完整的系统备份并备份所有客户托管密钥

**ID**：Azure 安全基准 9.2 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为虚拟机启用 Azure 备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |此策略可帮助审核是否为所有虚拟机启用了 Azure 备份服务。 Azure 备份是一个经济高效的一键式备份解决方案，可简化数据恢复，并且比其他云备份服务更易于启用。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[应为 Azure Database for MariaDB 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |此策略将审核未启用异地冗余备份的任何 Azure Database for MariaDB。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[应为 Azure Database for MySQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |此策略将审核未启用异地冗余备份的任何 Azure Database for MySQL。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[应为 Azure Database for PostgreSQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |此策略将审核未启用异地冗余备份的任何 Azure Database for PostgreSQL。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[应为 Azure SQL 数据库启用长期异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |此策略将审核未启用长期异地冗余备份的任何 Azure SQL 数据库。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="ensure-protection-of-backups-and-customer-managed-keys"></a>确保保护备份和客户管理的密钥

**ID**：Azure 安全基准 9.4 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[密钥保管库对象应可恢复](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |此策略审核密钥保管库对象是否不可恢复。 软删除功能有助于在给定的保留期（90 天）内有效地保留资源，即使在 DELETE 操作之后也是如此，同时提供对象已被删除的外观。 启用“清除保护”后，在长达 90 天的保留期到期之前，不能清除处于已删除状态的保管库或对象。 这些保管库和对象仍然可以恢复，从而向客户保证将遵循保留策略。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

## <a name="incident-response"></a>事件响应

### <a name="provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>提供安全事件联系人详细信息并针对安全事件配置警报通知

**ID**：Azure 安全基准 10.4 所有权：客户

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为订阅提供安全联系人电子邮件地址](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |输入电子邮件地址，以便在 Azure 安全中心检测到资源泄露时接收通知 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |
|[应为订阅提供安全联系人电话号码](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4d66858-c922-44e3-9566-5cdb7a7be744) |输入电话号码，以便在 Azure 安全中心检测到资源泄露情况时收到通知 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_phone_number.json) |

> [!NOTE]
> 特定 Azure Policy 定义的可用性在 Azure 政府和其他国家云中可能会有所不同。

## <a name="next-steps"></a>后续步骤

有关 Azure Policy 的其他文章：

- [法规符合性](../concepts/regulatory-compliance.md)概述。
- 请参阅[计划定义结构](../concepts/initiative-definition-structure.md)。
- 在 [Azure Policy 示例](./index.md)中查看其他示例。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
