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
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41c1c102e88e1712d561874aef87a6f22ed250a9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430212"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>“运行状况服务数据不是最新的”警报

## <a name="overview"></a>概述

本地计算机上 Azure AD Connect Health 监视定期将数据上载到 Azure AD Connect Health 服务的代理。 如果服务没有从代理接收数据，则该门户提供的信息将会过时。 若要突出显示此问题，服务会引发 "**运行状况服务数据不是最**新的" 警报。 在过去两小时内服务尚未收到完整数据时，将生成此警报。  

- 如果运行状况服务在过去两小时内只收到从服务器发送的**部分**数据类型，则会触发**警告**状态警报。 警告状态警报不会向已配置收件人触发电子邮件通知。 
- 如果运行状况服务在过去两小时内未从服务器收到任何数据类型，则会触发**错误**状态警报。 错误状态警报触发向已配置收件人发送电子邮件通知。

服务从运行在本地计算机上的代理中获取数据，具体取决于服务类型。 下表列出了在计算机上运行的代理、其功能以及服务生成的数据类型。 在某些情况下，该过程涉及到多个服务，因此其中任何一个服务可能就是问题所在。 

## <a name="understanding-the-alert"></a>了解警报

"**警报详细信息**" 边栏选项卡显示警报发生的时间和最后检测到的时间。 每两小时运行一次的后台进程会生成警报并对其进行重新评估。 在下面的示例中，第 9:59 AM 在03/10 发生了初始警报。 如果再次评估警报，则警报仍然存在于03/12 的 10:00 AM。 该边栏选项卡还详细介绍运行状况服务上次接收特定数据类型的时间。 
 
 ![Azure AD Connect Health 警报详细信息](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
下表将服务类型映射到相应的必需数据类型：

| 服务类型 | 代理（Windows 服务名称） | 目的 | 生成的数据类型  |
| --- | --- | --- | --- |  
| Azure AD Connect （同步） | Azure AD Connect Health Sync Insights 服务 | 收集特定于 AAD 连接的信息（连接器、同步规则等） | - AadSyncService-SynchronizationRules <br />  -AadSyncService <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync 监视服务 | 收集 AAD 连接特定的性能计数器、ETW 跟踪、文件 | 性能计数器 |
| AD DS | Azure AD Connect Health AD DS Insights 服务 | 执行综合测试，收集拓扑信息，复制元数据 |  -TopologyInfo-Json <br /> -TestData-Json （创建测试结果）   | 
|  | Azure AD Connect Health AD DS 监视服务 | 收集特定于添加的性能计数器、ETW 跟踪、文件 | -性能计数器  <br /> -TestData-Json （上传测试结果）  |
| AD FS | Azure AD Connect Health AD FS Diagnostics 服务 | 执行综合测试 | TestResult （创建测试结果） | 
| | Azure AD Connect Health AD FS Insights 服务  | 收集 ADFS 使用情况指标 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring 服务 | 收集 ADFS 特定的性能计数器、ETW 跟踪、文件 | TestResult （上传测试结果） |

## <a name="troubleshooting-steps"></a>故障排除步骤 

下面给出了诊断问题所需的步骤。 第一种是所有服务类型所共有的一组基本检查。 

> [!IMPORTANT] 
> 此警报遵守 Connect Health [数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)

* 请确保已安装最新版本的代理。 查看[发布历史记录](reference-connect-health-version-history.md)。 
* 请确保 Azure AD Connect Health 代理服务正在计算机上**运行**。 例如，适用于 AD FS 的 Connect Health 应该有三个服务。
  ![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* 请务必仔细审阅并满足[要求部分](how-to-connect-health-agent-install.md#requirements)。
* 使用[测试连接工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)来发现连接问题。
* 若有 HTTP 代理，请按照以下[配置步骤](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)操作。 


## <a name="next-steps"></a>后续步骤
如果上述任一步骤标识了问题，请修复该问题并等待警报解决。 警报后台进程每2小时运行一次，因此，最多需要2小时来解决警报。 

* [Azure AD Connect Health 数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.md)
