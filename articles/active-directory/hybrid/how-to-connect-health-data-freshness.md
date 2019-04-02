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
ms.openlocfilehash: 16794dfdcdc6ed9c2effe412237d2681fca4f394
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803283"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>“运行状况服务数据不是最新的”警报

## <a name="overview"></a>概述
Azure AD Connect Health 监视的本地计算机上的代理会定期将数据上载到 Azure AD Connect Health 服务。 如果服务未从代理接收数据，则门户中显示的信息将过时。 要突出显示该问题，该服务将显示“运行状况服务数据不是最新的”警报。 生成服务在过去的两个小时内未收到已完成的数据时。  

* **警告**触发状态警报，如果运行状况服务已收到仅**分部**过去的两个小时内从服务器发送的数据类型。 警告状态的警报不会触发向已配置的收件人的电子邮件通知。 
* **错误**如果运行状况服务未收到任何数据类型从服务器中过去两小时触发状态警报。 错误状态触发警报电子邮件通知发送给配置的收件人。

该服务在本地计算机运行的代理中获取的数据。 根据服务类型中下, 表列出了它们以及由服务生成的数据类型所执行的操作在计算机运行的代理。 在某些情况下，有多个服务所涉及的过程中，因此其中任何一个可能是问题所在。 

## <a name="understanding-the-alert"></a>了解警报
警报详细信息边栏选项卡指示引发并且上一次检测到警报的时间。 此警报是生成/重新-evaluated 由后台进程运行每隔两小时。 在下面的示例中，初始引发警报的 03/10 在上午 9:59。 它逐渐甚至在 03/12 存在 10:00 AM 重新评估警报。
在边栏选项卡还详细介绍了上一次运行状况服务接收特定的数据类型的时间。 
 
 ![Azure AD Connect Health 警报详细信息](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
下面是服务类型和相应的所需的数据类型的映射。

| 服务类型 | 代理 （Windows 服务名称） | 目的 | 生成的数据类型  |
| --- | --- | --- | --- |  
| Azure AD Connect （同步） | Azure AD Connect Health Sync Insights 服务 | 收集 AAD Connect 特定信息 （连接器，同步规则等。） | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync 监视服务 | 收集 (AAD Connect 特定) 性能计数器，ETW 跟踪文件 | 性能计数器 |
| AD DS | Azure AD Connect Health AD DS Insights 服务 | 执行综合测试、 收集拓扑信息，复制元数据 |  - Adds-TopologyInfo-Json <br /> 常见的 TestData-Json （创建测试结果）   | 
|  | Azure AD Connect Health AD DS 监视服务 | 收集 （特定于 ADDS 的） 性能计数器，ETW 跟踪文件 | 性能计数器  <br /> 常见的 TestData-Json （上传测试结果）  |
| AD FS | Azure AD Connect Health AD FS Diagnostics 服务 | 执行综合测试 | TestResult （创建测试结果） | 
| | Azure AD Connect Health AD FS Insights 服务  | 收集 ADFS 使用情况指标 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring 服务 | 收集 （特定于 ADFS 的） 性能计数器，ETW 跟踪文件 | TestResult （上传测试结果） |

## <a name="troubleshooting-steps"></a>疑难解答步骤 

下面提供了诊断问题所需的步骤。 第一个是一组通用的所有服务类型的基本检查。 下表列出了特定的步骤为每个服务类型和数据类型。 

> [!IMPORTANT] 
> 此警报遵守 Connect Health [数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)

* 请确保安装了代理的最新版本。 视图[发行历史记录](reference-connect-health-version-history.md)。 
* 请确保 Azure AD Connect Health 代理服务**运行**在计算机上。 例如，适用于 AD FS 的 Connect Health 应该有三个服务。
  ![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* 请务必仔细审阅并满足[要求部分](how-to-connect-health-agent-install.md#requirements)。
* 使用[测试连接工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)来发现连接问题。
* 若有 HTTP 代理，请按照以下[配置步骤](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)操作。 


## <a name="next-steps"></a>后续步骤
如果上述步骤的任何标识问题，修复此错误，并等待要解决的警报。 警报的后台进程运行每隔 2 小时，因此它将需要长达 2 小时，要解决该警报。 

* [Azure AD Connect Health 数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.md)
