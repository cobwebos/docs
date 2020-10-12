---
title: 按 Azure 服务列出的资源提供程序
description: 列出 Azure 资源管理器的所有资源提供程序命名空间，并显示该命名空间的 Azure 服务。
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: b740c8ee7c28bfeeaecc42121d0c7ce4a323ab3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330819"
---
# <a name="resource-providers-for-azure-services"></a>Azure 服务的资源提供程序

本文介绍资源提供程序命名空间如何映射到 Azure 服务。

## <a name="match-resource-provider-to-service"></a>将资源提供程序匹配到服务

| 资源提供程序命名空间 | Azure 服务 |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory 域服务](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService<sup>1</sup> | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft.Advisor | [Azure 顾问](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft.ApiManagement | [API 管理](../../api-management/index.yml) |
| Microsoft.AppConfiguration | [Azure 应用配置](../../azure-app-configuration/index.yml) |
| Microsoft.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Azure 证明服务 |
| Microsoft.Authorization<sup>1</sup> | [Azure 资源管理器](../index.yml) |
| Microsoft.Automation | [自动化](../../automation/index.yml) |
| Microsoft.AutonomousSystems | [自治系统](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft.AVS | [Azure VMware 解决方案](../../azure-vmware/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureData | SQL Server 注册表 |
| Microsoft.AzureStack | core |
| Microsoft.AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [批处理](../../batch/index.yml) |
| Microsoft.Billing<sup>1</sup> | [成本管理和计费](/azure/billing/) |
| Microsoft.BingMaps | [必应地图](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure 区块链服务](../../blockchain/workbench/index.yml) |
| Microsoft.BlockchainTokens | [Azure 区块链令牌](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft.Blueprint | [Azure 蓝图](../../governance/blueprints/index.yml) |
| Microsoft.BotService | [Azure 机器人服务](/azure/bot-service/) |
| Microsoft.Cache | [用于 Redis 的 Azure 缓存](../../azure-cache-for-redis/index.yml) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [内容分发网络](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [应用服务证书](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft.ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | 经典部署模型虚拟机 |
| Microsoft.ClassicInfrastructureMigrate | 经典部署模型迁移 |
| Microsoft.ClassicNetwork | 经典部署模型虚拟网络 |
| Microsoft.ClassicStorage | 经典部署模型存储 |
| Microsoft.ClassicSubscription<sup>1</sup> | 经典部署模型 |
| Microsoft.CognitiveServices | [认知服务](../../cognitive-services/index.yml) |
| Microsoft.Commerce<sup>1</sup> | core |
| Microsoft.Compute | [虚拟机](../../virtual-machines/index.yml)<br />[虚拟机规模集](../../virtual-machine-scale-sets/index.yml) |
| Microsoft. 使用量<sup>1</sup> | [成本管理](/azure/cost-management/) |
| Microsoft.ContainerInstance | [容器实例](../../container-instances/index.yml) |
| Microsoft.ContainerRegistry | [容器注册表](../../container-registry/index.yml) |
| Microsoft.ContainerService | [Azure Kubernetes 服务 (AKS)](../../aks/index.yml) |
| CostManagement<sup>1</sup> | [成本管理](/azure/cost-management/) |
| Microsoft.CostManagementExports | [成本管理](/azure/cost-management/) |
| Microsoft.CustomerLockbox | [Microsoft Azure 客户密码箱](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft.CustomProviders | [Azure Custom Providers](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](../../databox/index.yml) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [数据目录](../../data-catalog/index.yml) |
| Microsoft.DataFactory | [数据工厂](../../data-factory/index.yml) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Azure 数据库迁移服务](../../dms/index.yml) |
| Microsoft.DataProtection | 数据保护 |
| Microsoft.DataShare | [Azure Data Share](../../data-share/index.yml) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](../../mariadb/index.yml) |
| Microsoft.DBforMySQL | [Azure Database for MySQL](../../mysql/index.yml) |
| Microsoft.DBforPostgreSQL | [Azure Database for PostgreSQL](../../postgresql/index.yml) |
| Microsoft.DeploymentManager | [Azure 部署管理器](../templates/deployment-manager-overview.md) |
| Microsoft.DesktopVirtualization | [Windows 虚拟桌面](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Azure IoT 中心](../../iot-hub/index.yml)<br />[Azure IoT 中心设备预配服务](../../iot-dps/index.yml) |
| Microsoft.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft.DevTestLab | [Azure 实验室服务](../../lab-services/index.yml) |
| Microsoft.DigitalTwins | [Azure 数字孪生](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [应用服务](../../app-service/index.yml) |
| Microsoft.DynamicsLcs | [生命周期服务](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft.EnterpriseKnowledgeGraph | 企业知识图 |
| Microsoft.EventGrid | [事件网格](../../event-grid/index.yml) |
| Microsoft.EventHub | [事件中心](../../event-hubs/index.yml) |
| Microsoft.Features<sup>1</sup> | [Azure 资源管理器](../index.yml) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [Azure SAP HANA 大型实例](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure 专用 HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [适用于 FHIR 的 Azure API](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](../../storsimple/index.yml) |
| HybridNetwork  | [专用边缘区域](../../networking/edge-zones-overview.md) |
| Microsoft.ImportExport | [Azure 导入/导出](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft.IoTSpaces | [Azure 数字孪生](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.KeyVault | [密钥保管库](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [Azure Kubernetes 服务 (AKS)](../../aks/index.yml) |
| Microsoft.KubernetesConfiguration | [Azure Kubernetes 服务 (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure 数据资源管理器](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure 实验室服务](../../lab-services/index.yml) |
| Microsoft.Logic | [逻辑应用](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [机器学习工作室](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure 机器学习](../../machine-learning/index.yml) |
| Microsoft.Maintenance | [Azure 维护](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedNetwork | PaaS 服务托管的虚拟网络 |
| Microsoft.ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft.Management | [管理组](../../governance/management-groups/index.yml) |
| Microsoft.Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering<sup>1</sup> | core |
| Microsoft.Media | [媒体服务](../../media-services/index.yml) |
| Microsoft.Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.MixedReality | [Azure 空间定位点](../../spatial-anchors/index.yml) |
| Microsoft.NetApp | [Azure NetApp 文件](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [应用程序网关](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS 防护](../../virtual-network/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure 防火墙](../../firewall/index.yml)<br />[Azure Front Door 服务](../../frontdoor/index.yml)<br />[Azure 专用链接](../../private-link/index.yml)<br />[负载均衡器](../../load-balancer/index.yml)<br />[网络观察程序](../../network-watcher/index.yml)<br />[流量管理器](../../traffic-manager/index.yml)<br />[虚拟网络](../../virtual-network/index.yml)<br />[虚拟 WAN](../../virtual-wan/index.yml)<br />[VPN 网关](../../vpn-gateway/index.yml)<br /> |
| Microsoft 笔记本 | [Azure Notebook](https://notebooks.azure.com/help/introduction) |
| Microsoft.NotificationHubs | [通知中心](../../notification-hubs/index.yml) |
| Microsoft.ObjectStore | 对象存储 |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.Peering | [Azure 对等互连服务](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.Portal<sup>1</sup> | [Azure 门户](../../azure-portal/index.yml) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft.ProjectBabylon | [Azure 数据目录](../../data-catalog/overview.md) |
| Microsoft.Quantum | [Azure 量程](https://azure.microsoft.com/services/quantum/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft.RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure 中继](../../azure-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph<sup>1</sup> | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft.ResourceHealth | [Azure 服务运行状况](../../service-health/index.yml) |
| Microsoft.Resources<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [计划程序](../../scheduler/index.yml) |
| Microsoft.Search | [Azure 认知搜索](../../search/index.yml) |
| Microsoft.Security | [安全中心](../../security-center/index.yml) |
| Microsoft.SecurityGraph | [安全中心](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| SerialConsole<sup>1</sup> | [适用于 Windows 的 Azure 串行控制台](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [服务总线](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric 网格](../../service-fabric-mesh/index.yml) |
| Microsoft.Services | core |
| Microsoft.SignalRService | [Azure SignalR 服务](../../azure-signalr/index.yml) |
| Microsoft.SoftwarePlan | 许可证 |
| Microsoft.Solutions | [Azure 托管应用程序](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL 数据库](../../azure-sql/database/index.yml)<br /> [Azure SQL 托管实例](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [Azure 虚拟机中的 SQL Server](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [存储](../../storage/index.yml) |
| Microsoft.StorageCache | [Azure HPC 缓存](../../hpc-cache/index.yml) |
| Microsoft.StorageSync | [存储](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft.StreamAnalytics | [Azure 流分析](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support<sup>1</sup> | core |
| Microsoft.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure 时序见解](../../time-series-insights/index.yml) |
| Microsoft.Token | 令牌 |
| Microsoft.VirtualMachineImages | [Azure 映像生成器](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft.VMware | [Azure VMware 解决方案](../../azure-vmware/index.yml) |
| Microsoft.VMwareCloudSimple | [Azure VMware Solution by CloudSimple](../../vmware-cloudsimple/index.md) |
| Microsoft.VSOnline | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft.Web | [应用服务](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Microsoft Defender 高级威胁防护](../../security-center/security-center-wdatp.md) |
| Microsoft.WindowsESU | 扩展的安全更新 |
| Microsoft.WindowsIoT | [Windows 10 IoT Core Services](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor<sup>1</sup> | [Azure Monitor](../../azure-monitor/index.yml) |

<sup>1</sup> 默认已注册

## <a name="next-steps"></a>后续步骤

有关资源提供程序的详细信息，包括如何注册资源提供程序，请参阅 [Azure 资源提供程序和类型](resource-providers-and-types.md)。
