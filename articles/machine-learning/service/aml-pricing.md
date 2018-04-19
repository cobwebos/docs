---
title: Azure 机器学习服务的定价和计费
description: 本文包含有关 Azure 机器学习预览功能的定价和计费的常见问题解答
services: machine-learning
author: j-martens
ms.author: jmartens
manager: cgronlund
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 5e057f3fe4a4ff06e0acac29b3dcd11fa901fc40
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="pricing-and-billing-for-azure-machine-learning-services"></a>Azure 机器学习服务的定价和计费

有关完整详细信息，或想要查看示例帐单，请访问 [Azure 定价页](https://azure.microsoft.com/pricing/details/machine-learning-services/)。  

下面是一些关于计费和定价的常见问题解答。

## <a name="can-i-use-azure-machine-learning-for-free-during-preview"></a>在预览期间，是否可免费使用 Azure 机器学习？    

Azure Machine Learning Workbench 应用程序免费提供给 Azure 订阅者。

试验服务和模型管理提供免费层以及付费层，在使用公共预览版期间可按折扣价购买付费层。

## <a name="am-i-charged-based-on-how-many-experiments-i-run"></a>是否将根据运行的试验数量来收费？

否。借助试验服务，可以根据需要运行任意数目的试验，仅根据用户数量进行收费。 试验计算资源将单独收费。  我们建议你执行多次试验，以便找到最适合自己的解决方案的模型。 

## <a name="am-i-charged-based-on-how-many-times-my-web-services-is-called"></a>是否将根据调用 Web 服务的次数进行收费？

不会。 可以随时按需调用 Web 服务，不会产生任何的模型管理费用。 可以完全控制部署的缩放以满足应用程序需求。

## <a name="how-can-i-scale-the-units-i-purchase-in-the-azure-machine-learning-model-management"></a>如何在 Azure 机器学习模型管理中对购买单位进行缩放？

可以使用 Azure 门户或 CLI 向上或向下更改单位数量。 

## <a name="what-does-a-bill-look-like"></a>帐单的外观是什么样的？

帐单将按日生成。 为方便计费，一天从午夜 UTC 算起。 帐单将按月生成。 与 Azure 机器学习一起使用的任何 Azure 服务都会单独计费。 费用可能包括但不限于： 
- 计算费用
- HDInsight
- Azure 容器服务
- Azure 容器注册表 
- Azure Blob 存储
- Application Insights
- Azure 密钥保管库
- Visual Studio Team Services
- Azure 事件中心
- Azure 流分析 有关更多详细信息，或想要查看示例帐单，请访问 [Azure 定价页](https://azure.microsoft.com/pricing/details/machine-learning-services/)。 
