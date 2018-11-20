---
title: 使用适用于 Python 的机器学习数据准备 SDK 准备数据 - Azure
description: 了解如何使用适用于 Python 的 Azure 机器学习数据准备 SDK 来加载各种格式的数据，将其转换使其更易于使用，并将该数据写入模型可访问的位置。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: a315394ab394e7f4dfe528cf765c9ac5a65c80c4
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51276995"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>为 Azure 机器学习为建模准备数据
 
数据准备是机器学习工作流的重要部分。 如果模型有权清理更易于使用的格式中的数据，那么它们将会更加准确和高效。 

可以使用 [Azure 机器学习数据准备 SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) 在 Python 中准备数据。 

## <a name="data-preparation-pipeline"></a>数据准备管道

主要数据准备步骤如下：

1. [加载数据](how-to-load-data.md)，可以采用各种格式
2. 将其[转换](how-to-transform-data.md)为更可用的结构
3. 将该数据[写入](how-to-write-data.md)到模型可以访问的位置

![数据准备进程](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>后续步骤
使用 Azure 机器学习数据准备 SDK 查看数据准备的[示例笔记本](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb)。
