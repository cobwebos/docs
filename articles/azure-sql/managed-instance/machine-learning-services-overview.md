---
title: 'Azure SQL 托管实例 (预览版中的机器学习服务) '
description: 本文提供了有关 Azure SQL 托管实例的概述或机器学习服务。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: d7a3c86f3d9cf083a8746f753b8c5287c774a93e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263261"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Azure SQL 托管实例 (预览版中的机器学习服务) 

机器学习服务是 Azure SQL 托管实例 (预览) 的一项功能，可提供数据库内机器学习，同时支持 Python 和 R 脚本。 此功能包括 Microsoft Python 和 R 包，用于实现高性能预测分析和机器学习。 可以通过存储过程、包含 Python 或 R 语句的 T-sql 脚本、包含 T-sql 的 Python 或 R 代码，在脚本中使用关系数据。

> [!IMPORTANT]
> 机器学习服务是 Azure SQL 托管实例的一项功能，目前提供公共预览版。
> 此预览功能最初在美国、亚洲和澳大利亚的有限数量的区域中提供，以后添加其他区域。
>
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> [注册预览版](#signup)（见下）。

## <a name="what-is-machine-learning-services"></a>什么是机器学习服务？

机器学习服务在 Azure SQL 托管实例中，可以在数据库中执行 Python 和 R 脚本。 可以使用它来准备和清理数据、执行特征工程以及在数据库中定型、评估和部署机器学习模型。 此功能在数据所在的位置运行脚本，无需通过网络将数据传输到其他服务器。

使用 Azure SQL 托管实例中的 R/Python 支持机器学习服务：

- **运行 r 和 python 脚本来进行数据准备和常规用途的数据处理** -现在可以将 R/Python 脚本引入到 Azure SQL 托管实例你的数据所在的位置，而无需将数据移出到其他服务器来运行 R 和 Python 脚本。 您可以消除与延迟、安全性和合规性相关的数据移动和相关问题的需要。

- **在数据库中训练机器学习模型** -可以使用任何开源算法训练模型。 可以轻松地将培训扩展到整个数据集，而不是依赖于数据库中提取的示例数据集。

- **在存储过程中将模型和脚本部署到生产环境中** ，可以通过在 t-sql 存储过程中嵌入脚本和定型模型来操作化它们。 通过只调用存储过程，连接到 Azure SQL 托管实例的应用程序可以受益于这些模型中的预测和智能。 你还可以使用本机 T-sql 预测函数来操作模型，以便在高度并发的实时评分方案中快速计分。

Python 和 R 的基本分发包含在机器学习服务中。 除了用于 Python 的 [revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) 和 [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml) Microsoft 包，以及用于 R 的 [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler)、[MicrosoftML](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml)、[olapR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr) 和 [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) 外，还可以安装和使用开源包和框架，如 PyTorch、TensorFlow 和 scikit-learn。

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>注册预览版

此受限公共预览版受 [Azure 预览版条款的约束](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

如果你有兴趣加入预览计划并接受这些条款，则可以通过在处创建 Azure 支持票证来请求注册 [**https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) 。 

1. 选择以下选项：
   - 问题类型- **技术**
   - 订阅- *选择订阅*
   - 服务- **SQL 数据库托管实例**
   - 摘要- *输入请求的简短描述*
   - **机器学习服务 SQL 托管实例的问题类型 (预览) **
   - 问题子类型- **其他问题或 "如何" 问题**

1. 单击 " **下一步：解决方案**"。

1. 阅读有关预览版的信息，然后单击 " **详细信息**"。

1. 在 " **说明**" 中，输入你的请求的详细信息，包括要在预览版中注册的逻辑服务器名称、区域和订阅 ID。 根据需要输入其他详细信息。

1. 完成后，单击 " **下一步"：检查 + 创建**，然后单击 " **创建**"。

注册该计划后，Microsoft 将让你加入公共预览版，并为现有的或新的数据库启用机器学习服务。

建议不要将公共预览版 SQL 托管实例机器学习服务用于生产工作负载。

## <a name="next-steps"></a>后续步骤

- 请参阅 [SQL Server 机器学习服务的主要区别](machine-learning-services-differences.md)。
- 若要了解如何在机器学习服务中使用 Python，请参阅 [运行 python 脚本](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)。
- 若要了解如何在机器学习服务中使用 R，请参阅 [运行 r 脚本](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)。
- 有关其他 SQL 平台上的机器学习的详细信息，请参阅 [SQL 机器学习文档](https://docs.microsoft.com/sql/machine-learning/)。
