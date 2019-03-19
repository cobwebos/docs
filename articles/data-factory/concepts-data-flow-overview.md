---
title: Azure 数据工厂映射数据流概述
description: Azure 数据工厂中的映射数据流的概述说明
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 5064f47444b4ca6d9194ed66144938e6e3d51a4e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732358"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>什么是 Azure 数据工厂中的映射数据流？

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用映射数据流，数据工程师可以开发图形化数据转换逻辑，无需编写代码。 生成的数据流是使用横向扩展的 Azure Databricks 群集作为活动在 Azure 数据工厂管道内执行的。

Azure 数据工厂数据流的用途是在无需编码的情况下提供完全可视化的体验。 数据流将在你自己的执行群集上执行以进行横向扩展的数据处理。 Azure 数据工厂处理数据流作业的所有代码转换、路径优化以及执行。

首先在调试模式下创建数据流，以便能够以交互方式验证你的转换逻辑。 接下来，向管道中添加一个数据流活动以在管道调试中执行并测试你的数据流，或者在管道中使用“立即触发”来测试管道活动中的数据流。

然后，使用执行数据流活动的 Azure 数据工厂管道来计划并监视数据流活动。

数据流设计界面上的“调试模式”切换开关允许以交互方式构建数据转换。 调试模式为数据流构造提供了数据准备环境。
