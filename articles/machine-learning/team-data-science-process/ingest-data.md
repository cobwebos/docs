---
title: 将数据载入 Azure 存储环境进行分析 | Microsoft 文档
description: 将数据移入和移出 Azure Blob 存储
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 369b06ee22b30977affb3642f0d321e5d92e6c41
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="load-data-into-storage-environments-for-analytics"></a>将数据载入存储环境以进行分析
团队数据科学过程要求引入或载入各种不同存储环境中的数据在过程的每个阶段中都以最合适的方式进行处理或分析。 常用于处理的数据目标包括 Azure Blob 存储、SQL Azure 数据库、Azure VM 上的 SQL Server、 HDInsight (Hadoop) 和 Azure 机器学习。 

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

此**菜单**链接到介绍如何将数据引入这些目标环境中的主题，数据是在这些目标环境中进行存储和处理的。

技术和业务需求，以及初始位置、数据的格式和大小，将确定需要将数据引入其中以实现分析的目标的目标环境。 要求数据在多个环境之间移动以实现构建预测模型所需的各种任务，这样的方案是不常见的。 例如，此系列任务可能包括数据浏览、预处理、清理、向下采样和模型定型。

