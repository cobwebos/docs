---
title: "机器学习 Web 服务的日志记录 | Microsoft Docs"
description: "了解如何为机器学习 Web 服务启用日志记录。 日志记录提供用于帮助对 API 进行故障排除的其他信息。"
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/05/2016
ms.author: raymondl;garye
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f307a26bfbb55b395f4073f4368432ae69b867ae


---
# <a name="enable-logging-for-machine-learning-web-services"></a>为机器学习 Web 服务启用日志记录
文本档提供有关经典 Web 服务的日志记录功能的信息。 在 Web 服务中启用日志记录可提供错误编号和消息以外的其他信息，这些信息有助于对调用机器学习 API 进行故障排除。  

若要在 Azure 经典门户中启用 Web 服务中的日志记录：   

1. 登录到 [Azure 经典门户](https://manage.windowsazure.com/)
2. 在左侧的功能列中，单击“机器学习”。
3. 单击工作区，然后单击“Web 服务”。
4. 在 Web 服务列表中，单击 Web 服务的名称。
5. 在终结点列表中，单击终结点名称。
6. 单击“配置”。
7. 将“诊断跟踪级别”设置为“错误”或“全部”，然后单击“保存”。

在 Azure 机器学习 Web 服务门户中启用日志记录。

1. 登录到 [Azure 机器学习 Web 服务](https://services.azureml.net)门户。
2. 单击“经典 Web 服务”。
3. 在 Web 服务列表中，单击 Web 服务的名称。
4. 在终结点列表中，单击终结点名称。
5. 单击 **“配置”**。
6. 将“日志记录”设置为“错误”或“全部”，然后单击“保存”。

## <a name="the-effects-of-enabling-logging"></a>启用日志记录的效果
启用日志记录时，来自选定终结点的所有诊断和错误都记录到与用户的工作区链接的 Azure 存储帐户。 可在他们的工作区的 Azure 经典门户仪表板视图（“速览”部分底部）中看到此存储帐户。  

可使用可用于“浏览”Azure 存储帐户的多种工具中的任意一种查看日志。 最简单的方法可能只是导航到 Azure 经典门户中的存储帐户，然后单击“继续”。 然后，将看到名为“ml-diagnostics”的容器。 此容器承载与此存储帐户相关联的所有工作区的所有 Web 服务终结点的所有诊断信息。 

## <a name="log-blob-detail-information"></a>日志 blob 详细信息
容器中的每个 blob 承载完全用于以下操作之一的诊断信息：

* 执行 Batch-Execution 方法  
* 执行 Request-Response 方法  
* 初始化 Request-Response 容器

每个 blob 的名称都具有以下形式的前缀： 

{工作区 Id}-{Web 服务 Id}-{终结点 Id}/{日志类型}  

其中日志类型是以下值之一：  

* 批处理  
* score/requests  
* score/init  




<!--HONumber=Nov16_HO3-->


