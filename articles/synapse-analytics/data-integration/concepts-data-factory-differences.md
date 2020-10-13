---
title: 与 Azure 数据工厂的区别
description: 了解 Azure Synapse Analytics 的数据集成功能与 Azure 数据工厂的不同之处
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341310"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics 与 Azure 数据工厂中的数据集成

在 Azure Synapse 分析中，数据集成功能（如 Synapse 管道和数据流）基于 Azure 数据工厂的功能。 有关详细信息，请参阅 [什么是 Azure 数据工厂](../../data-factory/introduction.md)。 几乎所有功能完全相同或类似，并且文档在两个服务之间共享。 本文重点介绍 Azure 数据工厂与 Azure Synapse 之间的当前差异。

若要查看 Azure 数据工厂功能或文章是否适用于 Azure Synapse，请查看文章顶部的名字对象。

![适用于名字对象](../media/concepts-data-factory-differences/applies-to-moniker.png "适用于名字对象")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Azure 数据工厂中的功能未计划用于 Azure Synapse

以下功能在 Azure 数据工厂中提供，但未计划用于 Azure Synapse。

* 提升和转换 SSIS 包的能力。
* 雪花作为复制活动中的接收器和映射数据流。
* Azure 集成运行时的 "映射数据流生存时间" 设置。

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure 数据工厂不支持 azure Synapse 功能

以下功能在 Azure Synapse 中可用，但不计划用于 Azure 数据工厂。

* 对映射数据流的 Spark 作业监视仅在 Synapse 中可用。 在 Synapse 中，Spark 引擎包含在用户的订阅中，因此用户可以查看详细的 Spark 日志。 在 Azure 数据工厂中，作业执行发生在 Azure 数据工厂托管的 Spark 群集上。 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Synapse 中行为不同的 Azure 数据工厂功能

以下功能的行为方式不同，或当前在 Azure Synapse 中不存在。 

* 整理数据流
* 解决方案模板库
* Git 集成和本机 CI/CD 解决方案
* 与 Azure monitor 集成
* 发布后重命名资源
* Synapse 工作区中的混合集成运行时配置。 用户不能同时具有托管 VNet IR 和 Azure IR。
* Synapse 工作区之间的集成运行时共享

## <a name="next-steps"></a>后续步骤

通过了解如何将 [数据引入 Azure Data Lake Storage gen2 帐户](data-integration-data-lake.md)，开始在 Synapse 工作区中进行数据集成。
