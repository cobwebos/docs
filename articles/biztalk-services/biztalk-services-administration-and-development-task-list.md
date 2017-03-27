---
title: "BizTalk 服务中的管理和开发任务列表 | Microsoft Docs"
description: "帮助部署 Azure BizTalk 服务的规划和作业。"
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1013419f9283c3e81a6a6e8ed56a34d6ee5937ea


---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>BizTalk 服务中的管理和开发任务列表
## <a name="getting-started"></a>入门
使用 Microsoft Azure BizTalk 服务时，需考虑多个本地和基于云的组件。 请按照以下流程图开始操作：  

| 步骤 | 负责人 | 任务 | 相关链接 |
| --- | --- | --- | --- |
| 1. |管理员 |使用 Microsoft 帐户或组织帐户创建 Microsoft Azure 订阅 |[Azure 经典门户](http://go.microsoft.com/fwlink/p/?LinkID=213885) |
| 2. |管理员 |创建或预配 BizTalk 服务。 |[使用 Azure 经典门户创建 BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=302280) |
| 3. |管理员 |注册你个人或公司的 BizTalk 服务部署 |[在 BizTalk 服务门户中注册和更新 BizTalk 服务部署](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |管理员 |如果应用程序使用 BizTalk 适配器服务连接到本地业务线 (LOB) 系统或或队列/主题目标，则适用。  创建 Azure 服务总线命名空间。 向开发人员提供此命名空间、服务总线颁发者名称和服务总线颁发者密钥值。 |[如何：创建或修改服务总线服务命名空间](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)和[获取颁发者名称和颁发者密钥值](biztalk-issuer-name-issuer-key.md) |
| 5. |开发人员 |在 Visual Studio 中安装 SDK 并创建 BizTalk 服务项目。 |[安装 Azure BizTalk 服务 SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) 和[在 Azure 上创建丰富消息传递终结点](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |开发人员 |将 BizTalk 服务项目部署到托管在 Azure 上的 BizTalk 服务。 |[部署并刷新 BizTalk 服务项目](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |管理员 |使用 EDI 时适用。  可在 Microsoft Azure BizTalk 服务门户上添加合作伙伴和创建协议。 创建协议时，可将开发人员创建的桥和/或转换添加到协议设置。 |[在 BizTalk 服务门户中配置 EDI、AS2 和 EDIFACT](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |管理员 |使用 Azure 经典门户，监视 BizTalk 服务的运行状况，包括性能指标。 |[Biztalk 服务：“仪表板”、“监视”和“缩放”选项卡](http://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |管理员 |使用 Microsoft Azure BizTalk 服务门户，管理 BizTalk 服务使用的项目，并在桥文件处理消息时跟踪这些消息。 |[使用 BizTalk 服务门户](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |管理员 |创建备份计划以备份 BizTalk 服务。 |[BizTalk 服务中的业务连续性和灾难恢复](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>后续步骤
[教程和示例](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[在 Visual Studio 中创建项目](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[安装 Azure BizTalk 服务 SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>概念
[在 Visual Studio 中创建项目](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI、AS2 和 EDIFACT 消息传送（企业到企业）](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>其他资源
[添加源、目标和桥消息传送终结点](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[了解并创建消息映射和转换](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[使用 BizTalk 适配器服务 (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Azure BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=303664)




<!--HONumber=Nov16_HO3-->


