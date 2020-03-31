---
title: Azure AD Connect Health -“运行状况服务数据不是最新的”警报 | Microsoft Docs
description: 本文档介绍了“运行状况服务数据不是最新的”警报的原因以及如何对此警报进行故障排除。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897177"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>“运行状况服务数据不是最新的”警报

## <a name="overview"></a>概述

Azure AD 连接运行状况监视的本地计算机上代理定期将数据上载到 Azure AD 连接运行状况服务。 如果服务未从代理接收数据，则门户提供的信息将过时。 为了突出显示此问题，该服务将引发**运行状况服务数据不是最新的**警报。 当服务在过去两小时内未收到完整的数据时，将生成此警报。  

- 如果运行状况服务在过去两小时内仅收到从服务器发送**的部分**数据类型，则触发**警告**状态警报。 警告状态警报不会触发发送给已配置收件人的电子邮件通知。 
- 如果运行状况服务在过去两小时内未从服务器收到任何数据类型，则将触发**错误**状态警报。 错误状态警报触发电子邮件通知给已配置的收件人。

该服务从在本地计算机上运行的代理获取数据，具体取决于服务类型。 下表列出了在计算机上运行的代理、它们做什么以及服务生成的数据类型。 在某些情况下，该过程涉及多个服务，因此其中任何一个可能是罪魁祸首。 

## <a name="understanding-the-alert"></a>了解警报

"**警报详细信息"** 边栏选项卡显示警报发生和上次检测到的时。 每两小时运行一次的后台进程将生成并重新评估警报。 在下面的示例中，初始警报发生在 03/10 上午 9：59。 再次评估警报时，警报在 03/12 上午 10：00 仍然存在。 边栏选项卡还详细介绍了运行状况服务上次接收特定数据类型的时间。 
 
 ![Azure AD 连接运行状况警报详细信息](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
下表将服务类型映射到相应的所需数据类型：

| 服务类型 | 代理（Windows 服务名称） | 目的 | 生成的数据类型  |
| --- | --- | --- | --- |  
| Azure AD 连接（同步） | Azure AD Connect Health Sync Insights 服务 | 收集 AAD 连接特定信息（连接器、同步规则等） | - AadSync服务同步规则 <br />  - AadSync服务连接器 <br /> - AadSync服务-全球配置  <br />  - Aadsync服务-运行配置文件结果 <br /> - AadSync服务-服务配置 <br /> - AadSync服务-服务状态   |
|  | Azure AD Connect Health Sync 监视服务 | 收集 AAD 连接特定的 perf 计数器、ETW 跟踪、文件 | 性能计数器 |
| AD DS | Azure AD Connect Health AD DS Insights 服务 | 执行综合测试、收集拓扑信息、复制元数据 |  - 添加拓扑信息-日森 <br /> - 通用测试数据-Json（创建测试结果）   | 
|  | Azure AD Connect Health AD DS 监视服务 | 收集特定于 ADDS 的 perf 计数器、ETW 跟踪、文件 | - 性能计数器  <br /> - 通用测试数据-Json（上传测试结果）  |
| AD FS | Azure AD Connect Health AD FS Diagnostics 服务 | 执行综合测试 | 测试结果（创建测试结果） | 
| | Azure AD Connect Health AD FS Insights 服务  | 收集 ADFS 使用指标 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring 服务 | 收集特定于 ADFS 的 perf 计数器、ETW 跟踪、文件 | 测试结果（上传测试结果） |

## <a name="troubleshooting-steps"></a>疑难解答步骤 

诊断问题所需的步骤如下所示。 第一个是一组所有服务类型共有的基本检查。 

> [!IMPORTANT] 
> 此警报遵守 Connect Health [数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)

* 确保安装了最新版本的代理。 查看[发布历史记录](reference-connect-health-version-history.md)。 
* 确保 Azure AD 连接运行状况代理服务在计算机上**运行**。 例如，适用于 AD FS 的 Connect Health 应该有三个服务。
  ![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* 请务必仔细审阅并满足[要求部分](how-to-connect-health-agent-install.md#requirements)。
* 使用[测试连接工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)来发现连接问题。
* 若有 HTTP 代理，请按照以下[配置步骤](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)操作。 


## <a name="next-steps"></a>后续步骤
如果上述任何步骤发现问题，请修复它并等待警报解决。 警报后台进程每 2 小时运行一次，因此最多需要 2 小时才能解决警报。 

* [Azure AD 连接运行状况数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.md)
