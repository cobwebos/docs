---
title: 使用 Azure 数据工厂从多个容器中复制文件 |Microsoft Docs
description: 了解如何使用解决方案模板使用 Azure 数据工厂从多个容器复制文件。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635111"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>使用 Azure 数据工厂复制多个容器中的文件

本文介绍可用于将文件从文件存储之间的多个容器复制的解决方案模板。 例如，您可以使用它来从 AWS S3 的数据湖迁移到 Azure Data Lake Store。 或者，您可以使用模板从一个 Azure Blob 存储帐户的所有内容复制到另一个。

> [!NOTE]
> 如果你想要将文件复制从单个容器，它是使用更加高效[复制数据工具](copy-data-tool.md)若要创建包含单个复制活动的管道。 这篇文章中的模板是多个所需的该简单的方案。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板枚举来自源存储存储的容器。 然后将这些容器复制到目标存储区。

该模板包含三个活动：
- **GetMetadata**扫描你的源存储存储，并获取容器列表。
- **ForEach**获取容器列表从**GetMetadata**活动循环访问该列表并将每个容器传递到复制活动。
- **复制**源存储存储的每个容器复制到目标存储区。

该模板定义两个参数：
- *SourceFilePath*是您的数据源存储，可以获取容器的列表的路径。 在大多数情况下，该路径是包含多个容器文件夹的根目录。 此参数的默认值为 `/`。
- *DestinationFilePath*是其中的文件将会复制到目标存储区中的路径。 此参数的默认值为 `/`。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到**复制文件存储之间的多个文件容器**模板。 创建**新建**到源存储存储的连接。 源存储存储是你想要将文件从多个容器中复制。

    ![与源建立新的连接](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 创建**新建**将存储到目标存储存储的连接。

    ![与目标建立新的连接](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. 选择**使用此模板**。

    ![使用此模板](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 可以看到管道中的，如以下示例所示：

    ![显示管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. 选择**调试**，输入**参数**，然后选择**完成**。

    ![运行管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 查看结果。

    ![查看结果](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>后续步骤

- [数据库中的 Azure 数据工厂中使用控制表的大容量复制](solution-template-bulk-copy-with-control-table.md)

- [将多个容器使用 Azure 数据工厂中的文件](solution-template-copy-files-multiple-containers.md)