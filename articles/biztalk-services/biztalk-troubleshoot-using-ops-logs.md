---
title: "利用操作日志对 BizTalk 服务进行故障排除 | Microsoft Docs"
description: "利用操作日志对 BizTalk 服务进行故障排除。 MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 7d3a357e1a3929153288a9d99e21f2379bcac891
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk 服务：利用操作日志进行故障排除

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>什么是操作日志
操作日志是一项管理服务功能，可让你查看针对 Azure 服务（包括 BizTalk 服务）执行的操作的历史日志。 这允许查看与 BizTalk 服务订阅的管理操作相关的历史数据，时间可以追溯到 180 天前。

> [!NOTE]
> 此功能只捕获 BizTalk 服务的管理操作的日志，例如服务的启动时间、备份时间等。 可使用 [BizTalk 服务 REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx) 跟踪此类操作。 有关使用管理服务跟踪的操作的完整列表，请参阅[使用 Azure 管理服务跟踪的操作](#bizops)。<br/><br/>
> 此功能不会捕获与 BizTalk 服务运行时相关的活动（例如桥处理的消息等）的日志。 若要查看这些日志，请使用 BizTalk 服务门户中的“跟踪”视图。 有关详细信息，请参阅[跟踪消息](http://msdn.microsoft.com/library/azure/hh949805.aspx)。
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>查看 BizTalk 服务操作日志
1. 在门户中，选择“管理服务”，并选择“操作日志”选项卡。
2. 可以基于订阅、日期范围、服务类型（例如 BizTalk 服务）、服务名称或操作的状态（“成功”、“失败”）等不同参数来筛选日志。
3. 选择复选标记可以查看筛选后的列表。 下图显示了与 testbiztalkservice 相关的活动：![查看操作日志][ViewLogs] 
4. 要查看有关特定操作的详细信息，请选择相应的行，并在底部的任务栏中单击“详细信息”。

## <a name="bizops"></a>使用 Azure 管理服务跟踪的操作
下表列出了使用 Azure 管理服务跟踪的操作：

| 操作名称 | 任务 |
| --- | --- |
| CreateBizTalkService |用于创建新 BizTalk 服务的操作 |
| DeleteBizTalkService |用于删除 BizTalk 服务的操作 |
| RestartBizTalkService |用于重新启动 BizTalk 服务的操作 |
| StartBizTalkService |用于启动 BizTalk 服务的操作 |
| StopBizTalkService |用于停止 BizTalk 服务的操作 |
| DisableBizTalkService |用于禁用 BizTalk 服务的操作 |
| EnableBizTalkService |用于启用 BizTalk 服务的操作 |
| BackupBizTalkService |用于备份 BizTalk 服务的操作 |
| RestoreBizTalkService |用于从指定的备份还原 BizTalk 服务的操作 |
| SuspendBizTalkService |用于挂起 BizTalk 服务的操作 |
| ResumeBizTalkService |用于恢复 BizTalk 服务的操作 |
| ScaleBizTalkService |用于扩展或缩减 BizTalk 服务的操作 |
| ConfigUpdateBizTalkService |用于更新 BizTalk 服务配置的操作 |
| ServiceUpdateBizTalkService |用于将 BizTalk 服务升级或降级为不同版本的操作 |
| PurgeBackupBizTalkService |用于清除超过保留期的 BizTalk 服务备份的操作 |

## <a name="see-also"></a>另请参阅
* [Backup BizTalk Service](http://go.microsoft.com/fwlink/p/?LinkID=325584)（备份 BizTalk 服务）
* [从备份还原 BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk 服务：开发人员版、基本版、标准版和高级版图表](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk 服务：预配](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk 服务：预配状态图表](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Biztalk 服务：“仪表板”、“监视”和“缩放”选项卡](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk 服务：限制](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk 服务：颁发者名称和颁发者密钥](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [如何开始使用 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

