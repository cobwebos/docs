---
title: 从 Azure Blob 存储导入:模块参考
titleSuffix: Azure Machine Learning service
description: 了解本主题介绍如何使用 Azure 机器学习 service 中的 "从 Azure Blob 存储导入" 模块从 Azure blob 存储读取数据, 以便在机器学习试验中使用数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fea64070c496379351bb75f2a38aba9b4db70dcd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128717"
---
# <a name="import-from-azure-blob-storage-module"></a>从 Azure Blob 存储模块导入

本文介绍了 Azure 机器学习服务的可视界面 (预览) 的模块。

使用此模块可从 Azure blob 存储读取数据, 以便在机器学习试验中使用数据。  

Azure Blob 服务用于存储大量数据, 包括二进制数据。 可以使用 HTTP 或 HTTPS 从任何位置访问 Azure blob。 根据 blob 存储的类型, 可能需要进行身份验证。 

- 公共 blob 可供任何人访问, 也可由具有 SAS URL 的用户访问。
- 专用 blob 需要登录名和凭据。

从 blob 存储导入要求将数据存储在使用**块 blob**格式的 blob 中。 存储在 blob 中的文件必须使用逗号分隔 (CSV) 或制表符分隔 (TSV) 格式。 读取该文件时, 记录和任何适用的属性标题将作为行加载到作为数据集的内存中。


我们强烈建议你在导入数据前对其进行分析, 以确保架构符合预期。 导入过程扫描一些头行来确定架构, 但后面的行可能包含额外的列或导致错误的数据。



## <a name="manually-set-properties-in-the-import-data-module"></a>手动设置 "导入数据" 模块中的属性

以下步骤说明如何手动配置导入源。

1. 将 "**导入数据**" 模块添加到试验中。 可以在接口中的**数据输入和输出**中找到此模块

2. 对于 "**数据源**", 请选择 " **Azure Blob 存储**"。

3. 对于 "**身份验证类型**", 请选择 "**公共 (SAS URL)" (** 如果你知道信息已作为公共数据源提供)。 SAS URL 是公共访问的时间绑定 URL, 可通过使用 Azure 存储实用程序生成。

    否则, 选择 "**帐户**"。

4. 如果你的数据位于可以使用 SAS URL 访问的**公共**blob 中, 则无需其他凭据, 因为 URL 字符串包含下载和身份验证所需的所有信息。

    在 " **URI** " 字段中, 键入或粘贴定义帐户和公共 blob 的完整 URI。



5. 如果你的数据位于**专用**帐户, 则必须提供凭据, 包括帐户名称和密钥。

    - 对于 "**帐户名称**", 请键入或粘贴包含要访问的 blob 的帐户的名称。

        例如, 如果存储帐户的完整 URL 为`http://myshared.blob.core.windows.net`, 则键入。 `myshared`

    - 对于 "**帐户密钥**", 请粘贴与帐户关联的存储访问密钥。

        如果你不知道访问密钥, 请参阅本文中的 "管理 Azure 存储帐户" 一节:[关于 Azure 存储帐户](https://docs.microsoft.com/azure/storage/storage-create-storage-account)。

6. 对于**容器、目录或 blob 的路径**, 键入要检索的特定 blob 的名称。

    例如, 如果将名为**data01**的文件上传到名为**mymldata**的帐户中的容器**trainingdata** , 则该文件的完整 URL 将为: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt`。

    因此, 在**容器、目录或 blob**的字段路径中, 你可以键入:`trainingdata/data01.csv`

    若要导入多个文件, 您可以使用`*`通配符 (星号) `?`或 (问号)。

    例如, 假设容器`trainingdata`包含多个兼容格式的文件, 则可以使用以下规范读取以`data`开头的所有文件, 并将它们连接到单个数据集:

    `trainingdata/data*.csv`

    不能在容器名称中使用通配符。 如果需要从多个容器导入文件, 请使用每个容器的 "**导入数据**" 模块的单独实例, 然后使用 "[添加行](./add-rows.md)" 模块合并数据集。

    > [!NOTE]
    > 如果选择了选项, 请**使用缓存的结果**, 对容器中的文件所做的任何更改都不会触发试验中数据的刷新。

7. 对于 " **blob 文件格式**", 请选择一个选项来指示存储在 blob 中的数据的格式, 以便 Azure 机器学习可以相应地处理数据。 支持以下格式:

    - **CSV**:逗号分隔值 (CSV) 是用于在 Azure 机器学习中导出和导入文件的默认存储格式。 如果数据已包含标题行, 请确保选择 "**文件具有标题行**" 选项, 否则标头将被视为数据行。

       

    - **TSV**:制表符分隔值 (TSV) 是许多机器学习工具使用的格式。 如果数据已包含标题行, 请确保选择 "**文件具有标题行**" 选项, 否则标头将被视为数据行。

       

    - **ARFF**:此格式支持以 Weka 工具集使用的格式导入文件。 

   

8. 运行试验。


## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 