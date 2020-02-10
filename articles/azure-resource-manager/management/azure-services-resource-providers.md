---
title: Azure 服务的资源提供程序
description: 列出 Azure 资源管理器的所有资源提供程序命名空间，并显示该命名空间的 Azure 服务。
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 64c1d3c6f30a4f817abbea5001852df3eeeb9327
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109348"
---
# <a name="resource-providers-for-azure-services"></a>Azure 服务的资源提供程序

本文介绍如何将资源提供程序命名空间映射到 Azure 服务。

## <a name="match-resource-provider-to-service"></a>将资源提供程序与服务匹配

| 资源提供程序命名空间 | Azure 服务 |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory 域服务](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure 顾问](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [API 管理](../../api-management/index.yml) |
| Microsoft.AppConfiguration | core |
| Microsoft.Attestation | Azure 证明服务 |
| Microsoft.Authorization | [Azure 资源管理器](../index.yml) |
| Microsoft.Automation | [自动化](../../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureStack | core |
| Microsoft.Batch | [批处理](../../batch/index.yml) |
| Microsoft.Billing | [成本管理和计费](/azure/billing/) |
| Microsoft.BingMaps | [必应地图](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure 区块链服务](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Azure 蓝图](/azure/governance/blueprints/) |
| Microsoft.BotService | [Azure 机器人服务](/azure/bot-service/) |
| Microsoft.Cache | [用于 Redis 的 Azure 缓存](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [内容交付网络](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [应用服务证书](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | 经典部署模型虚拟机 |
| Microsoft.ClassicInfrastructureMigrate | 经典部署模型迁移 |
| Microsoft.ClassicNetwork | 经典部署模型虚拟网络 |
| Microsoft.ClassicStorage | 经典部署模型存储 |
| Microsoft.ClassicSubscription | 经典部署模型 |
| Microsoft.CognitiveServices | [认知服务](/azure/cognitive-services/) |
| Microsoft.Commerce | core |
| Microsoft.Compute | [虚拟机](/azure/virtual-machines/)<br />[虚拟机规模集](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption | [成本管理](/azure/cost-management/) |
| Microsoft.ContainerInstance | [容器实例](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [容器注册表](/azure/container-registry/) |
| Microsoft.ContainerService | [Azure Kubernetes 服务 (AKS)](/azure/aks/) |
| Microsoft.CostManagement | [成本管理](/azure/cost-management/) |
| CostManagementExports | [成本管理](/azure/cost-management/) |
| Microsoft.CustomerLockbox | Microsoft Azure 的客户密码箱 |
| Microsoft.CustomProviders | [Azure 自定义提供程序](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Data Box Edge](../../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [数据目录](/azure/data-catalog/) |
| Microsoft.DataFactory | [数据工厂](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Store](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Azure 数据库迁移服务](/azure/dms/) |
| DataShare | [Azure 数据共享](/azure/data-share/) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Azure Database for MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| DesktopVirtualization | [Windows 虚拟桌面](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Azure 部署管理器](../templates/deployment-manager-overview.md) |
| Microsoft.Devices | [IoT 中心](/azure/iot-hub/)<br />[IoT 中心设备预配服务](/azure/iot-dps/) |
| DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure 实验室服务](../../lab-services/index.yml) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [应用服务](/azure/app-service/) |
| Microsoft.EnterpriseKnowledgeGraph | 企业知识图 |
| Microsoft.EventGrid | [事件网格](/azure/event-grid/) |
| Microsoft.EventHub | [事件中心](../../event-hubs/index.yml) |
| Microsoft.Features | [Azure 资源管理器](../index.yml) |
| Microsoft.Genomics | [Microsoft 基因组学](/azure/genomics/) |
| Microsoft.GuestConfiguration | [Azure 策略](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [Azure 上的 SAP HANA](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure 专用 HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [用于 FHIR 的 Azure API](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Azure 导入/导出](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Azure 数字孪生](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [Key Vault](../../key-vault/index.yml) |
| Microsoft.Kusto | [Azure 数据资源管理器](../../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure 实验室服务](../../lab-services/index.yml) |
| Microsoft.Logic | [逻辑应用](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [机器学习工作室](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [机器学习服务](../../machine-learning/index.yml) |
| Microsoft.ManagedIdentity | [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/index.yml) |
| ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [管理组](/azure/governance/management-groups/) |
| Microsoft.Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering | core |
| Microsoft.Media | [媒体服务](../../media-services/index.yml) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-overview.md) |
| MixedReality | [Azure 空间定位点](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp 文件](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [虚拟网络](../../virtual-network/index.yml)<br />[负载均衡器](../../load-balancer/index.yml)<br />[应用程序网关](../../application-gateway/index.yml)<br />[Azure DNS](../../dns/index.yml)<br />[ExpressRoute](../../expressroute/index.yml)<br />[VPN 网关](../../vpn-gateway/index.yml)<br />[流量管理器](../../traffic-manager/index.yml)<br />[网络观察程序](../../network-watcher/index.yml)<br />[Azure 防火墙](../../firewall/index.yml)<br />[Azure Front Door 服务](../../frontdoor/index.yml)<br />[Azure 堡垒](/azure/bastion/) |
| Microsoft.NotificationHubs | [通知中心](../../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.Peering | Microsoft Azure 对等互连服务 |
| Microsoft.PolicyInsights | [Azure 策略](../../governance/policy/index.yml) |
| Microsoft.Portal | [Azure 门户](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Site Recovery](../../site-recovery/index.yml) |
| Microsoft.Relay | [Azure 中继](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Azure 资源关系图](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | [Azure 服务运行状况](../../service-health/index.yml)|
| Microsoft.Resources | [Azure 资源管理器](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [计划程序](/azure/scheduler/) |
| Microsoft.Search | [Azure 搜索](../../search/index.yml) |
| Microsoft.Security | [安全中心](../../security-center/index.yml) |
| SecurityInsights | [Azure Sentinel](/azure/sentinel/) |
| SerialConsole | [Azure 串行控制台](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [服务总线](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric 网格](../../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Azure SignalR 服务](../../azure-signalr/index.yml) |
| Microsoft.SiteRecovery | [Site Recovery](../../site-recovery/index.yml) |
| Microsoft.Solutions | [Azure 托管应用程序](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL 数据库](../../sql-database/index.yml)<br />[SQL 数据仓库](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [Azure 虚拟机中的 SQL Server](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [存储](../../storage/index.yml) |
| Microsoft.StorageCache | [Azure HPC 缓存](/azure/hpc-cache/) |
| Microsoft.StorageSync | [存储](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [流分析](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support | core |
| Microsoft.TimeSeriesInsights | [时序见解](../../time-series-insights/index.yml) |
| Microsoft.VirtualMachineImages | [Azure 映像生成器](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft.VMwareCloudSimple | [Azure VMware 解决方案（按 CloudSimple）](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [应用服务](../../app-service/index.yml)<br />[函数](../../azure-functions/index.yml) |
| Microsoft.WindowsIoT | [Windows 10 IoT Core Services](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="next-steps"></a>后续步骤

有关资源提供程序的详细信息，请参阅[Azure 资源提供程序和类型](resource-providers-and-types.md)
