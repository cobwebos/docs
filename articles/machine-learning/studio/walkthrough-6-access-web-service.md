---
title: 步骤 6：访问机器学习 Web 服务 | Microsoft Docs
description: 开发预测解决方案演练的步骤 6：访问活动的 Azure 机器学习 Web 服务。
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: be63a04d0fe71039b19eb6bc0678a319f622a811
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836698"
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>演练步骤 6：访问 Azure 机器学习 Web 服务

这是演练的最后一步，[在 Azure 机器学习中开发预测分析解决方案](walkthrough-develop-predictive-solution.md)

1. [创建机器学习工作区](walkthrough-1-create-ml-workspace.md)
2. [上传现有数据](walkthrough-2-upload-data.md)
3. [创建新试验](walkthrough-3-create-new-experiment.md)
4. [定型和评估模型](walkthrough-4-train-and-evaluate-models.md)
5. [部署 Web 服务](walkthrough-5-publish-web-service.md)
6. **访问 Web 服务**

- - -
在本演练的上一步中，已部署了使用信用风险预测模型的 Web 服务。 现在用户能够向其发送数据并收到结果。 

Web 服务是一种 Azure Web 服务，可以两种方式之一使用 REST API 接收和返回数据：  

* **请求/响应** - 用户通过使用 HTTP 协议，向服务发送信用数据的一个或多个行，而服务会使用一个或多个结果集进行响应。
* **Batch 执行** - 用户将信用数据的一个或多个行存储在 Azure blob 中，然后将 blob 位置发送到服务。 服务将对输入 blob 中数据的所有行进行评分，将结果存储到另一 blob 中，并返回该容器的 URL。  

访问经典 Web 服务最快且最简单的方法是通过 [Azure ML 请求-响应服务 Web 应用](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)或 [Azure ML 批处理执行服务 Web 应用模板](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)。

这些 Web 应用模板可生成自定义 Web 应用，该应用了解 Web 服务的输入数据及返回结果。 所需操作就是提供 Web 服务和数据的访问权限，余下操作由模板完成。

有关使用 Web 应用模板的详细信息，请参阅[使用 Azure 机器学习 Web 服务与 Web 应用模板](consume-web-service-with-web-app-template.md)。

还可使用 R、C# 和 Python 编程语言中提供的起始代码开发自定义应用程序，以访问 Web 服务。

可以在[如何使用 Azure 机器学习 Web 服务](consume-web-services.md)找到完整的详细信息。

