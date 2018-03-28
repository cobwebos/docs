---
title: 机器学习 Web 服务的日志记录 | Microsoft Docs
description: 了解如何为机器学习 Web 服务启用日志记录。 日志记录提供用于帮助对 API 进行故障排除的其他信息。
services: machine-learning
documentationcenter: ''
author: aashishb
ms.author: aashishb
manager: hjerez
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 1e04ef638c46ef0f3b40fd56d27ba3673565bdc7
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="enable-logging-for-machine-learning-web-services"></a>为机器学习 Web 服务启用日志记录
本文档提供有关机器学习 Web 服务的日志记录功能的信息。 日志记录可提供错误编号和消息以外的其他信息，这些信息有助于对调用机器学习 API 进行故障排除。  

## <a name="how-to-enable-logging-for-a-web-service"></a>如何为 Web 服务启用日志记录

在 [Azure 机器学习 Web 服务](https://services.azureml.net)门户中启用日志记录。 

1. 在 [https://services.azureml.net](https://services.azureml.net) 中登录到 Azure 机器学习 Web 服务门户。 若要在经典 Web 服务中到达此门户，可在机器学习工作室中单击机器学习 Web 服务页上的“新 Web 服务体验”。

   ![新 Web 服务体验链接](./media/web-services-logging/new-web-services-experience-link.png)

2. 在顶部菜单栏，单击“Web 服务”用作新 Web 服务，或单击“经典 Web 服务”用作经典 Web 服务。

   ![选择新 Web 服务或经典 Web 服务](./media/web-services-logging/select-web-service.png)

3. 对于新 Web 服务，单击 Web 服务名称。 对于经典 Web 服务，单击 Web 服务名称，然后单击下一页上的相应终结点。

4. 单击顶部菜单中的“配置”。

5. 将“启用日志记录”选项设置为“错误”（用于仅记录错误）或“全部”（用于记录完整日志）。

   ![选择日志记录级别](./media/web-services-logging/enable-logging.png)

6. 单击“ **保存**”。

7. 对于经典 Web 服务，创建“ml-diagnostics”容器。

   所有 Web 服务日志都保存在与此 Web 服务关联的存储帐户中名为“ml-diagnostics”的 blob 容器中。 对于新 Web 服务，容器会在你首次访问该 Web 服务时创建。 对于经典 Web 服务，需在容器尚不存在时进行创建。 

   1. 在[Azure 门户](https://portal.azure.com)中，转到与此 Web 服务关联的存储帐户。

   2. 单击“Blob 服务”下的“容器”。

   3. 如果容器“ml-diagnostics”不存在，请单击“+容器”，将容器命名为“ml-diagnostics”，然后选择“访问类型”作为“Blob”。 单击“确定”。

      ![选择日志记录级别](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> 对于经典 Web 服务，机器学习工作室中的 Web 服务仪表板还具有启用日志记录的开关。 但是，由于日志记录现在通过 Web 服务门户进行托管，因此需通过该门户启用日志记录，如本文所述。 如果已在工作室中启用日志记录，则需在 Web 服务门户中禁用日志记录并再次启用。


## <a name="the-effects-of-enabling-logging"></a>启用日志记录的效果
启用日志记录后，对于来自 Web 服务终结点的诊断和错误，它们都记录在与用户的工作区链接的 Azure 存储帐户中的“ml-diagnostics”blob 容器中。 此容器承载与此存储帐户相关联的所有工作区的所有 Web 服务终结点的所有诊断信息。

可使用可用于浏览 Azure 存储帐户的多种工具中的任意一种查看日志。 最简单的方法是导航到 Azure 门户中的存储帐户，单击“门户”，然后单击容器“ml-diagnostics”。  

## <a name="log-blob-detail-information"></a>日志 blob 详细信息
容器中的每个 blob 承载完全匹配以下操作之一的诊断信息：

* 执行 Batch-Execution 方法  
* 执行 Request-Response 方法  
* 初始化 Request-Response 容器

每个 blob 的名称都具有以下形式的前缀： 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


其中“日志类型”是以下值之一：  

* 批处理  
* score/requests  
* score/init  

