---
title: "如何增加 Azure 机器学习 Web 服务的并发 | Microsoft Docs"
description: "了解如何通过添加更多终结点来增加 Azure 机器学习 Web 服务的并发。"
services: machine-learning
documentationcenter: 
author: neerajkh
manager: srikants
editor: cgronlun
keywords: "Azure 机器学习, Web 服务, 操作化, 扩展, 终结点, 并发"
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: neerajkh
translationtype: Human Translation
ms.sourcegitcommit: c54b16517b6f08ed8bfddfb6e2c793ec0f292899
ms.openlocfilehash: 678f747fd1b2e5017d1859fd594c3b4bc465ea41


---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>通过添加更多终结点来扩展 Azure 机器学习 Web 服务
> [!NOTE]
> 本主题介绍适用于经典机器学习 Web 服务的技术。 
> 
> 

默认情况下，每个已发布的 Web 服务配置为支持 20 个并发请求，并且最高可达 200 个并发请求。 尽管 Azure 经典门户提供了设置此值的方法，Azure 机器学习自动优化设置以为 Web 服务提供最佳性能，并忽略门户值。 

如果计划调用带有高于并发调用值 200 所支持的负载的 API，应在同一个 Web 服务上创建多个终结点。 然后可在所有终结点上随机分发负载。

Web 服务的扩展是常见任务。 扩展的一些原因是为了支持超过 200 个并发请求、通过多个终结点增加可用性或为 Web 服务提供单独的终结点。 通过 [Azure 经典门户](https://manage.windowsazure.com/)或 [Azure 机器学习 Web 服务](https://services.azureml.net/)门户为同一个 Web 服务添加其他终结点，可增加规模。

有关添加新终结点的详细信息，请参阅[创建终结点](machine-learning-create-endpoint.md)。

请记住，如果不使用相应的高速率调用 API，使用高并发数可能有害。 如果在针对高负载配置的 API 上放置相对较低的负载，则可能看到偶发的超时和/或延迟峰值。

同步 API 通常在需要低延迟的情况下使用。 此处的延迟表示 API 完成一个请求所需的时间，不考虑任何网络延迟。 假设你有一个带有 50 毫秒延迟的 API。 若要使用限制级别高和最大并发调用 = 20 完全消耗可用容量，每秒需要调用此 API 20 * 1000 / 50 = 400 次。 进一步扩展，假设 50 毫秒的延迟，最大并行调用 200 允许每秒调用 API 4000 次。

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png



<!--HONumber=Jan17_HO5-->


