---
title: Azure AD Connect Health -“运行状况服务数据不是最新的”警报 | Microsoft Docs
description: 本文档介绍了“运行状况服务数据不是最新的”警报的原因以及如何对此警报进行故障排除。
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: ca9f23703315424fcf08350ae3111a20dd94c160
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233219"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>“运行状况服务数据不是最新的”警报

## <a name="overview"></a>概述
<li>如果 Azure AD Connect Health 在两小时内未从服务器收到所有数据点，则它会生成数据刷新警报。 警报标题为“运行状况服务数据不是最新的”。 </li>
<li>如果 Connect Health 在两个小时内没有收到从服务器发送的部分数据元素，则会引发“警告”状态的警报。 “警告”状态的警报不会触发向租户管理员发送电子邮件通知。 </li>
<li>如果 Connect Health 在两个小时内没有收到从服务器发送的任何数据元素，则会引发“错误”状态的警报。 “错误”状态的警报会触发向租户管理员发送电子邮件通知。 </li>

>[!IMPORTANT] 
> 此警报遵守 Connect Health [数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>疑难解答步骤 
* 请务必仔细审阅并满足[要求部分](how-to-connect-health-agent-install.md#requirements)。
* 使用[测试连接工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)来发现连接问题。
* 如果具有 HTTP 代理，请遵循[此处的配置步骤](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)。 

### <a name="connect-health-for-sync"></a>适用于同步的 Connect Health

| 数据元素 | 疑难解答步骤 |
| --- | --- | 
| PerfCounter | - [Azure 服务终结点的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [已筛选或禁用针对出站流量的 SSL 检查](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [运行代理的服务器上的防火墙端口](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [如果启用了 IE 增强安全性，允许指定的网站](https://technet.microsoft.com/en-us/windows/ms537180(v=vs.60)) |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - 基于 IP 地址的出站连接，请参阅 [Azure IP 范围](https://www.microsoft.com/download/details.aspx?id=41653) <br /> - [Azure 服务终结点的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [运行代理的服务器上的防火墙端口](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>适用于 ADFS 的 Connect Health

可通过额外步骤来验证 AD FS，并按照 [AD FS 帮助](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282)中的工作流操作。

| 数据元素 | 疑难解答步骤 |
| --- | --- | 
| PerfCounter, TestResult | - [Azure 服务终结点的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [已筛选或禁用针对出站流量的 SSL 检查](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [运行代理的服务器上的防火墙端口](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [如果启用了 IE 增强安全性，允许指定的网站](https://technet.microsoft.com/en-us/windows/ms537180(v=vs.60)) |
|  Adfs-UsageMetrics | 基于 IP 地址的出站连接，请参阅 [Azure IP 范围](https://www.microsoft.com/download/details.aspx?id=41653) | 

### <a name="connect-health-for-adds"></a>适用于 ADDS 的 Connect Health

| 数据元素 | 疑难解答步骤 |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Azure 服务终结点的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [已筛选或禁用针对出站流量的 SSL 检查](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [运行代理的服务器上的防火墙端口](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [如果启用了 IE 增强安全性，允许指定的网站](https://technet.microsoft.com/en-us/windows/ms537180(v=vs.60)) <br />  - 基于 IP 地址的出站连接，请参阅 [Azure IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)  |


## <a name="next-steps"></a>后续步骤
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.md)
