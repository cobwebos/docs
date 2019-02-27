---
title: Azure AD Connect Health -“运行状况服务数据不是最新的”警报 | Microsoft Docs
description: 本文档介绍了“运行状况服务数据不是最新的”警报的原因以及如何对此警报进行故障排除。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ad829b976d8b712ee8027c89fb618c6c07de1bc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429003"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>“运行状况服务数据不是最新的”警报

## <a name="overview"></a>概述
Azure AD Connect Health 监视的本地计算机上的代理会定期将数据上载到 Azure AD Connect Health 服务。 如果服务未从代理接收数据，则门户中显示的信息将过时。 要突出显示该问题，该服务将显示“运行状况服务数据不是最新的”警报。 当服务在过去两小时内未收到数据时将生成此信息。  

* 如果 Connect Health 在两个小时内没有收到从服务器发送的部分数据元素，则会引发“警告”状态的警报。 “警告”状态的警报不会触发向租户管理员发送电子邮件通知。
* 如果 Connect Health 在两个小时内没有收到从服务器发送的任何数据元素，则会引发“错误”状态的警报。 “错误”状态的警报会触发向租户管理员发送电子邮件通知。


## <a name="troubleshooting-steps"></a>疑难解答步骤 

> [!IMPORTANT] 
> 此警报遵守 Connect Health [数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)

* 确保在计算机上运行 Azure AD Connect Health 代理服务。 例如，适用于 AD FS 的 Connect Health 应该有三个服务。  
  ![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* 请务必仔细审阅并满足[要求部分](how-to-connect-health-agent-install.md#requirements)。
* 使用[测试连接工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)来发现连接问题。
* 若有 HTTP 代理，请按照以下[配置步骤](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)操作。 

警报详细信息边栏选项卡列出了服务器中缺少的数据元素。 下表将有助于进一步缩小问题范围。 
### <a name="connect-health-for-sync"></a>适用于同步的 Connect Health

| 数据元素 | 疑难解答步骤 |
| --- | --- | 
| PerfCounter | - [Azure 服务终结点的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [已筛选或禁用针对出站流量的 SSL 检查](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [运行代理的服务器上的防火墙端口](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - [Azure 服务终结点的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [运行代理的服务器上的防火墙端口](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>适用于 ADFS 的 Connect Health

可通过额外步骤来验证 AD FS，并按照 [AD FS 帮助](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282)中的工作流操作。

| 数据元素 | 疑难解答步骤 |
| --- | --- | 
| PerfCounter, TestResult | - [Azure 服务终结点的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [已筛选或禁用针对出站流量的 SSL 检查](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [运行代理的服务器上的防火墙端口](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
|  Adfs-UsageMetrics | 基于 IP 地址的出站连接，请参阅 [Azure IP 范围](https://www.microsoft.com/download/details.aspx?id=41653) | 

### <a name="connect-health-for-adds"></a>适用于 ADDS 的 Connect Health

| 数据元素 | 疑难解答步骤 |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Azure 服务终结点的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [运行代理的服务器上的防火墙端口](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |


## <a name="next-steps"></a>后续步骤
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.md)
