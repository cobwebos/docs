---
title: FedRAMP 和 DoD SRG 审核作用域中的 azure 服务
description: 本文包含有关在 Azure 公有云和 Azure 政府版，展示了哪些 FedRAMP （中等与表高） 和给定的服务已达到 DoD SRG （影响级别 2、 4 或 5） 审核作用域。
author: davib
ms.author: davib
ms.date: 5/17/2019
ms.topic: article
ms.service: security
ms.reviewer: rochiou
ms.openlocfilehash: 6f92c609cbc9c5ba7d5d185b369bbf0a9d798a3e
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743099"
---
# <a name="azure-services-by-fedramp-and-dod-cc-srg-audit-scope"></a>Azure 服务的 FedRAMP 和 DoD CC SRG 审核作用域

Microsoft 政府云服务满足要求苛刻的要求和美国国防部，从信息影响级别 2 到 5 的美国联邦风险和授权管理计划 (FedRAMP)。 通过部署受保护的服务包括 Azure 政府版，Office 365 美国政府和 Dynamics 365 政府版，美国联邦国防机构可以利用丰富的符合服务。

本文范围内云服务的详细的的列表跨 Azure 和 Azure Government 的 FedRAMP 和 DoD CC SRG 符合性产品/服务。

#### <a name="terminologysymbols-used"></a>使用的术语/符号

* DoD SRG CC = 国防部云计算安全要求指南
* IIL = 信息影响级别
* FedRAMP = 联邦风险和授权管理计划  
* : heavy_check_mark: = 指示服务已获得此审核作用域。

## <a name="azure-public-services-by-audit-scope"></a>通过审核作用域的 azure 公共服务
| _上次更新时间：2019 年 5_ |

| Azure 服务| 抄送 DoD SRG IIL 2 | FedRAMP 中等 | FedRAMP 高 | 计划内的 2019 |
| ------------ |:---------------:|:----------------:|:------------:|:------------:|
| [API 管理](https://azure.microsoft.com/services/api-management/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [应用服务：API 应用](https://azure.microsoft.com/services/app-service/api/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [应用服务：移动应用](https://azure.microsoft.com/services/app-service/mobile/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [应用服务：Web 应用](https://azure.microsoft.com/services/app-service/web/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [应用程序网关](https://azure.microsoft.com/services/application-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [自动化](https://azure.microsoft.com/services/automation/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory （免费和基本）](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory （高级版 P1 + P2）](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) |  |  |  | :heavy_check_mark: |
| [Azure Active Directory 域服务](https://azure.microsoft.com/services/active-directory-ds/) | |  |  | :heavy_check_mark: |
| [Azure 高级威胁防护](https://azure.microsoft.com/features/azure-advanced-threat-protection/) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure 顾问](https://azure.microsoft.com/services/advisor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure 容器服务](https://docs.microsoft.com/azure/container-service/) |  |  |  | :heavy_check_mark: |
| [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/) |  |  |  | :heavy_check_mark: |
| [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure 数据库迁移服务](https://azure.microsoft.com/services/database-migration/) |  |  |  | :heavy_check_mark: |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) ** |  |  |  |  |
| [Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure DevOps (以前称为 VSTS)](https://azure.microsoft.com/services/devops/) | |  |  | :heavy_check_mark: |
| [Azure 开发测试实验室](https://azure.microsoft.com/services/devtest-lab/) |  |  |  | :heavy_check_mark: |
| [Azure DNS](https://azure.microsoft.com/services/dns/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure 文件同步](https://azure.microsoft.com/services/storage/files/) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure 的第一道防线](https://azure.microsoft.com/services/frontdoor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure 信息保护](https://azure.microsoft.com/services/information-protection/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Intune](https://docs.microsoft.com/intune/what-is-intune) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/) | |  |  | :heavy_check_mark: |
| [Azure Maps](https://azure.microsoft.com/services/azure-maps/) |  |  |  | :heavy_check_mark: |
| [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Monitor](https://azure.microsoft.com/services/monitor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Policy](https://azure.microsoft.com/services/azure-policy/) |  |  |  | :heavy_check_mark: |
| [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure 搜索](https://azure.microsoft.com/services/search/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Azure Service Manager (RDFE)](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [备份](https://azure.microsoft.com/services/backup/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [批处理](https://azure.microsoft.com/services/batch/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [BizTalk 服务](https://azure.microsoft.com/services/biztalk-services/) |  |  |  |  |
| [Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [云服务](https://azure.microsoft.com/services/cloud-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [认知服务：计算机视觉](https://azure.microsoft.com/services/cognitive-services/computer-vision/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [认知服务：内容审查器](https://azure.microsoft.com/services/cognitive-services/content-moderator/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [认知服务：自定义视觉](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [认知服务：Face](https://azure.microsoft.com/services/cognitive-services/face/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [认知服务：语言理解](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [认知服务：QnA Maker](https://azure.microsoft.com/services/cognitive-services/qna-maker/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [认知服务：语音服务](https://azure.microsoft.com/services/cognitive-services/directory/speech/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [认知服务：文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [认知服务：文本翻译](https://azure.microsoft.com/services/cognitive-services/speech-translation/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [认知服务：视频索引器](https://azure.microsoft.com/services/media-services/video-indexer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [容器实例](https://azure.microsoft.com/services/container-instances/) |  |  |  | :heavy_check_mark: |
| [容器注册表](https://azure.microsoft.com/services/container-registry/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [内容交付网络](https://azure.microsoft.com/services/cdn/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [数据目录](https://azure.microsoft.com/services/data-catalog/) |  |  |  | :heavy_check_mark: |
| [数据工厂](https://azure.microsoft.com/services/data-factory/) |  |  |  | :heavy_check_mark: |
| [Data Lake Analytics](https://azure.microsoft.com/services/data-lake-analytics/) |  |  |  |  |
| [事件网格](https://azure.microsoft.com/services/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Defender 高级威胁防护](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [事件中心](https://azure.microsoft.com/services/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [流](https://docs.microsoft.com/flow/getting-started) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [函数](https://azure.microsoft.com/services/functions/) |  |  |  | :heavy_check_mark: |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [导入/导出](https://azure.microsoft.com/services/storage/import-export/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [IoT Central](https://azure.microsoft.com/services/iot-central/) |  |  |  | :heavy_check_mark: |
| [IoT 中心](https://azure.microsoft.com/services/iot-hub/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Key Vault](https://azure.microsoft.com/services/key-vault/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [负载均衡器](https://azure.microsoft.com/services/load-balancer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [逻辑应用](https://azure.microsoft.com/services/logic-apps/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [机器学习服务](https://azure.microsoft.com/services/machine-learning-service/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [机器学习工作室](https://azure.microsoft.com/services/machine-learning-studio/) |  |  |  | :heavy_check_mark: |
| [媒体服务](https://azure.microsoft.com/services/media-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Azure 门户](https://azure.microsoft.com/features/azure-portal/)| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | |  |  | :heavy_check_mark: |
| [Microsoft Genomics](https://azure.microsoft.com/services/genomics/) |  |  |  | :heavy_check_mark: |
| [Microsoft PowerApps](https://docs.microsoft.com/powerapps/powerapps-overview) | | :heavy_check_mark: | :heavy_check_mark: |  |
| [Microsoft Stream](https://docs.microsoft.com/stream/overview) | | :heavy_check_mark: | :heavy_check_mark: |  |
| [多重身份验证](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [网络观察程序](https://azure.microsoft.com/services/network-watcher/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [网络观察程序流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) |  |  |  | :heavy_check_mark: |
| [通知中心](https://azure.microsoft.com/services/notification-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Redis 缓存](https://azure.microsoft.com/services/cache/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [计划程序](https://azure.microsoft.com/services/scheduler/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [安全中心](https://azure.microsoft.com/services/security-center/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [服务总线](https://azure.microsoft.com/services/service-bus/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Service Fabric](https://azure.microsoft.com/services/service-fabric/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [SQL 数据仓库](https://azure.microsoft.com/services/sql-data-warehouse/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [SQL 数据库](https://azure.microsoft.com/services/sql-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [SQL Server Stretch Database](https://azure.microsoft.com/services/sql-server-stretch-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [存储：Blob](https://azure.microsoft.com/services/storage/blobs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [存储：磁盘 （包括托管磁盘）](https://azure.microsoft.com/services/storage/disks/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [存储：文件](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [存储：队列](https://azure.microsoft.com/services/storage/queues/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [存储：表](https://azure.microsoft.com/services/storage/tables/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [StorSimple](https://azure.microsoft.com/services/storsimple/) |  |  |  | :heavy_check_mark: |
| [流分析](https://azure.microsoft.com/services/stream-analytics/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [时序见解](https://azure.microsoft.com/services/time-series-insights/) |  |  |  | :heavy_check_mark: |
| [流量管理器](https://azure.microsoft.com/services/traffic-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [虚拟机规模集](https://azure.microsoft.com/services/virtual-machine-scale-sets/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [虚拟机 （包括保留实例）](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [虚拟网络](https://azure.microsoft.com/services/virtual-network/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [VPN 网关](https://azure.microsoft.com/services/vpn-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

&ast;&ast; Azure Databricks SOC 证明包括仅限 SOC 2 类型 2 报表

## <a name="azure-government-services-by-audit-scope"></a>通过审核作用域的 azure 政府版服务
| _上次更新时间：2019 年 5_ |

| Azure 服务 | 抄送 DoD SRG IIL 2 | 抄送 DoD SRG IIL 4 | 抄送 DoD SRG IIL 5 | FedRAMP 高 | 计划内的 2019
| ------------- |:---------------:|:---------------:|:---------------:|:------------:|:------------:
| [API 管理](https://azure.microsoft.com/services/api-management/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [应用服务：API 应用](https://azure.microsoft.com/services/app-service/api/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [应用服务：移动应用](https://azure.microsoft.com/services/app-service/mobile/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [应用服务：Web 应用](https://azure.microsoft.com/services/app-service/web/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [应用程序网关](https://azure.microsoft.com/services/application-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [自动化](https://azure.microsoft.com/services/automation/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: |
| [Azure Active Directory （免费和基本）](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Active Directory （高级版 P1 + P2）](https://azure.microsoft.com/services/active-directory/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 顾问](https://azure.microsoft.com/services/advisor/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure DB for MySQL](https://azure.microsoft.com/services/mysql/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [用于 PostgreSQL 的 Azure DB](https://azure.microsoft.com/services/postgresql/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/) |  |  |  |  | :heavy_check_mark:
| [Azure DNS](https://azure.microsoft.com/services/dns/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 文件同步](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure 的第一道防线](https://azure.microsoft.com/services/frontdoor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 信息保护](https://azure.microsoft.com/services/information-protection/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: |
| [Azure Lab Services](https://azure.microsoft.com/services/lab-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Monitor](https://azure.microsoft.com/services/monitor/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Policy](https://azure.microsoft.com/services/azure-policy/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: **&ast;** | :heavy_check_mark: |
| [Azure 安全中心](https://azure.microsoft.com/services/security-center/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Azure Service Manager (RDFE)](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [备份](https://azure.microsoft.com/services/backup/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [批处理](https://azure.microsoft.com/services/batch/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [云服务](https://azure.microsoft.com/services/cloud-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [认知服务：自定义语音](https://aka.ms/speechcustomization) |  |  |  |  |
| [认知服务：语音翻译](https://azure.microsoft.com/services/cognitive-services/directory/speech/) |  |  |  |  |
| [认知服务：文本翻译](https://azure.microsoft.com/services/cognitive-services/speech-translation/) |  |  |  |  | :heavy_check_mark:
| [事件中心](https://azure.microsoft.com/services/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [流](https://docs.microsoft.com/flow/getting-started) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [函数](https://azure.microsoft.com/services/functions/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [导入/导出](https://azure.microsoft.com/services/storage/import-export/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [IoT 中心](https://azure.microsoft.com/services/iot-hub/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: |
| [Key Vault](https://azure.microsoft.com/services/key-vault/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [负载均衡器](https://azure.microsoft.com/services/load-balancer/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [逻辑应用](https://azure.microsoft.com/services/logic-apps/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [媒体服务](https://azure.microsoft.com/services/media-services/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Microsoft Azure 门户](https://azure.microsoft.com/features/azure-portal/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: **&ast;** | :heavy_check_mark: |
| [Microsoft Graph](https://docs.microsoft.com/graph/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Microsoft PowerApps](https://docs.microsoft.com/powerapps/powerapps-overview) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [Microsoft Stream](https://docs.microsoft.com/stream/overview) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [多重身份验证](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [网络观察程序](https://azure.microsoft.com/services/network-watcher/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [通知中心](https://azure.microsoft.com/services/notification-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: **&ast;** | :heavy_check_mark: |
| [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Redis 缓存](https://azure.microsoft.com/services/cache/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [计划程序](https://azure.microsoft.com/services/scheduler/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [服务总线](https://azure.microsoft.com/services/service-bus/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://azure.microsoft.com/services/service-fabric/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [SQL 数据仓库](https://azure.microsoft.com/services/sql-data-warehouse/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [SQL 数据库](https://azure.microsoft.com/services/sql-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [SQL Server Stretch Database](https://azure.microsoft.com/services/sql-server-stretch-database/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [存储：Blob](https://azure.microsoft.com/services/storage/blobs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [存储：磁盘 （包括托管磁盘）](https://azure.microsoft.com/services/storage/disks/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [存储：文件](https://azure.microsoft.com/services/storage/files/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [存储：队列](https://azure.microsoft.com/services/storage/queues/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [存储：表](https://azure.microsoft.com/services/storage/tables/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [StorSimple](https://azure.microsoft.com/services/storsimple/) | :heavy_check_mark: | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark:
| [流量管理器](https://azure.microsoft.com/services/traffic-manager/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟机规模集](https://azure.microsoft.com/services/virtual-machine-scale-sets/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟机](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟网络](https://azure.microsoft.com/services/virtual-network/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN 网关](https://azure.microsoft.com/services/vpn-gateway/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

**&ast;** 某些服务等 Azure 门户和 Azure 资源管理器不存储或处理 DoD CC SRG IIL 5 内容，但是仍继承 IIL5 控件集。
