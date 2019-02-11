---
title: Azure Batch AI 将发生什么情况？ | Microsoft Docs
description: 了解 Azure Batch AI 和 Azure 机器学习服务计算选项发生的情况。
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436868"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Azure Batch AI 将发生什么情况？

Azure Batch AI 服务即将在 3 月停用。 Batch AI 的大规模培训和评分功能现在可在 [Azure 机器学习服务](../machine-learning/service/overview-what-is-azure-ml.md)中获取，该服务已于 2018 年 12 月 4 日正式发布。

除了许多其他机器学习功能外，Azure 机器学习服务还包括基于云的托管计算目标，用于培训、部署和对机器学习模型评分。 此计算目标称为 [Azure 机器学习计算](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)。 [开始迁移并立即使用](#migrate)。 可以通过 Azure 机器学习服务 [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md)、命令行界面和 [Azure 门户](../machine-learning/service/quickstart-get-started.md)与 Azure 机器学习服务进行交互。

## <a name="support-timeline"></a>支持时间线

| 日期 | Batch AI 服务支持的详细信息 |
| ---- |-----------------|
| December&nbsp;14&#x2c;&nbsp;2018| 可以像以前一样继续使用现有的 Azure Batch AI 订阅。 但是，无法再注册新订阅，并且不会对此服务进行新的投资。|
| March&nbsp;31&#x2c;&nbsp;2019 | 此日期之后，现有的 Batch AI 订阅不再有效。 |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>如何迁移？

要避免应用程序中断并确保可使用最新功能，请在 2019 年 3 月 31 日之前执行以下步骤：

1. 创建 Azure 机器学习服务工作区并开始使用：
    + [基于 Python 的快速入门](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [基于 Azure 门户的快速入门](../machine-learning/service/quickstart-get-started.md)

1. 为模型定型设置 [Azure 机器学习计算](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)。

1. 更新脚本以使用 Azure 机器学习计算。

## <a name="support"></a>支持

希望迁移到更全面的 [Azure 机器学习服务](https://aka.ms/aml-docs)的现有客户可以获得支持。

如果 Azure 机器学习服务无法满足你的需求，而 Batch AI 服务中提供支持的功能，请向支持团队发起 Batch AI 支持请求，将你的订阅列入允许列表，以使用 Batch AI 直到服务停用。

## <a name="next-steps"></a>后续步骤

+ 请阅读 [Azure 机器学习服务概述](../machine-learning/service/overview-what-is-azure-ml.md)。

+ 请使用 Azure 机器学习服务[为模型定型配置计算目标](../machine-learning/service/how-to-set-up-training-targets.md)。

+ 请查看 [Azure 路线图](https://azure.microsoft.com/updates/)以了解其他 Azure 服务更新。
