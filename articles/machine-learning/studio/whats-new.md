---
title: Azure 机器学习中的新增功能 | Microsoft 文档
description: Azure 机器学习中的新增功能。
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: e0bf53670895c81379dcc2cd58542a8e3b3bc585
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Azure 机器学习新增功能

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>Microsoft Azure 机器学习更新的 2017 年 3 月版提供以下功能：



* Azure 机器学习 BES 作业的专用容量

    机器学习批处理池处理使用 [Azure Batch](../../batch/batch-technical-overview.md)服务为 Azure 机器学习批处理执行服务提供客户管理的缩放。 通过批处理池处理可创建 Azure Batch 池，以在该池中提交批处理作业，并以可预测方式执行这些作业。

    有关详细信息，请参阅[适用于机器学习作业的 Azure Batch 服务](dedicated-capacity-for-bes-jobs.md)。


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Microsoft Azure 机器学习更新的 2016 年 8 月版提供以下功能：
* 经典 Web 服务现可在新的 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/)门户中管理，这里提供管理 Web 服务所有方面的一个地方。    
  * 提供 Web 服务[使用情况统计](manage-new-webservice.md)。
  * 使用示例数据简化了对 Azure 机器学习远程请求调用的测试。
  * 提供新的 Batch 执行服务测试页面，并附有示例数据和作业提交历史记录。
  * 提供更简单的终结点管理。

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>Microsoft Azure 机器学习更新的 2016 年 7 月版提供以下功能：
* Web 服务现在作为通过 [Azure 资源管理器](../../azure-resource-manager/resource-group-overview.md)界面管理的 Azure 资源进行管理，支持以下增强功能：
  * 新 [REST API](https://msdn.microsoft.com/library/azure/Dn950030.aspx) 可部署和管理基于 Resource Manager 的 Web 服务。
  * 新 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/)门户提供管理 Web 服务所有方面的一个地方。
* 使用利用 Web 服务的 Resource Manager 资源提供程序基于 Resource Manager 的 API 合并新的基于订阅、多区域 Web 服务部署模型。
* 使用新的用于计费的 Resource Manager RP 引入新的[定价计划](https://azure.microsoft.com/pricing/details/machine-learning/)和计划管理功能。
  * 现在可[将 Web 服务部署到多个区域](how-to-deploy-to-multiple-regions.md)，无需在每个区域创建订阅。
* 提供 Web 服务[使用情况统计](manage-new-webservice.md)。
* 使用示例数据简化了对 Azure 机器学习远程请求调用的测试。
* 提供新的 Batch 执行服务测试页面，并附有示例数据和作业提交历史记录。

此外，机器学习工作室已更新，支持部署到新 Web 服务模型，或继续部署到经典 Web 服务模型。 

