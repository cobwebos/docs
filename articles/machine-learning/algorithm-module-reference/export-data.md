---
title: 导出数据：模块引用
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "导出数据" 模块，将管道中的结果、中间数据和工作数据保存到 Azure 机器学习之外的云存储目标。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b7b4b9de1e91279243e35f1b71f1ef6d2244e9e0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693711"
---
# <a name="export-data-module"></a>导出数据模块

本文介绍了 Azure 机器学习服务的可视界面（预览）的模块。

使用此模块可将管道中的结果、中间数据和工作数据保存到 Azure 机器学习以外的云存储目标。

此模块支持将数据导出或保存到以下 cloud data services：


- **导出到 Azure Blob 存储**：将数据保存到 azure 中的 Blob 服务。 可以在受保护的应用程序数据存储中公开或保存 Blob 服务中的数据。

  
## <a name="how-to-configure-export-data"></a>如何配置导出数据

1. 将**导出数据**模块添加到接口中的管道。 您可以在 "**输入和输出**" 类别中找到此模块。

2. 将**导出数据**连接到包含要导出的数据的模块。

3. 双击 "**导出数据**" 以打开 "**属性**" 窗格。

4. 对于 "**数据目标**"，请选择要在其中保存数据的云存储类型。 如果对此选项进行了任何更改，则会重置所有其他属性。 因此，请务必先选择此选项！

5. 提供访问指定存储帐户所需的帐户名称和身份验证方法。

    仅**导出到 Azure Blob 存储**是个人预览版中的唯一选项。 下面的示例演示如何设置模块。
    1. Azure blob 服务用于存储大量数据，包括二进制数据。 有两种类型的 blob 存储：公共 blob 和需要登录凭据的 blob。

    2. 对于 "**身份验证类型**"，请选择 "**公用（SAS）" （** 如果你知道存储支持通过 SAS URL 进行访问）。

          SAS URL 是一种特殊类型的 URL，可使用 Azure 存储实用程序生成，仅限时可用。  它包含身份验证和下载所需的所有信息。

        对于 " **URI**"，请键入或粘贴定义帐户和公共 blob 的完整 URI。

        对于文件格式，支持 CSV 和 TSV。

    3. 对于专用帐户，选择 "**帐户**"，并提供帐户名称和帐户密钥，以便管道可以写入存储帐户。

         - **帐户名称**：键入或粘贴要在其中保存数据的帐户的名称。 例如，如果存储帐户的完整 URL 是 `http://myshared.blob.core.windows.net` 的，则键入 `myshared`。

        - **帐户密钥**：粘贴与帐户关联的存储访问密钥。

        -  **容器、目录或 blob 的路径**：键入存储导出数据的 blob 的名称。 例如，若要将管道的结果保存到名为**mymldata**的帐户的容器**预测**中名为**results01**的新 blob，则将 `http://mymldata.blob.core.windows.net/predictions/results01.csv` blob 的完整 URL。

            因此，在**容器、目录或 blob**的字段路径中，将按如下所示指定容器和 blob 名称： `predictions/results01.csv`

        - 如果指定不存在的 blob 名称，Azure 将为你创建 blob。

       -  写入现有 blob 时，可以通过设置属性 " **Azure blob 存储写入模式**" 来指定覆盖 blob 的当前内容。 默认情况下，此属性设置为 "**错误**"，这意味着只要找到同名的现有 blob 文件，就会引发错误。


    4. 对于 " **blob 文件的文件格式**"，请选择数据的存储格式。

        - **CSV**：逗号分隔值（CSV）是默认存储格式。 要连同数据一起导出列标题，请选择 "**写入 blob 标题行**" 选项。  有关 Azure 机器学习中使用的以逗号分隔的格式的详细信息，请参阅[转换为 CSV](./convert-to-csv.md)。

        - **TSV**：制表符分隔值（TSV）格式与许多机器学习工具兼容。 要连同数据一起导出列标题，请选择 "**写入 blob 标题行**" 选项。  

 
    5. **使用缓存的结果**：如果想要避免每次运行管道时将结果重写到 blob 文件，请选择此选项。 如果没有对模块参数进行其他更改，则管道仅在第一次运行模块时或者在数据发生更改时写入结果。

    6. 运行管道。

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 