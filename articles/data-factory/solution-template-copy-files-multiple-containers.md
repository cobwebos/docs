---
title: 从多个容器复制文件
description: 了解如何使用解决方案模板通过 Azure 数据工厂从多个容器复制文件。
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
ms.openlocfilehash: fffe356225fc813b027bf1d6e29b3afe19b7fb01
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890787"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>使用 Azure 数据工厂复制多个容器中的文件

本文介绍可用于在文件存储之间复制多个容器中的文件的解决方案模板。 例如，可以使用它将 data lake 从 AWS S3 迁移到 Azure Data Lake Store。 或者，可以使用模板将所有内容从一个 Azure Blob 存储帐户复制到另一个。

> [!NOTE]
> 如果要从单个容器复制文件，则使用[复制数据工具](copy-data-tool.md)创建包含单个复制活动的管道会更有效。 本文中的模板超出了这一简单方案的需求。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板枚举源存储存储中的容器。 然后，它会将这些容器复制到目标存储中。

该模板包含三个活动：
- **GetMetadata**扫描源存储存储并获取容器列表。
- **ForEach**从**GetMetadata**活动获取容器列表，然后循环访问该列表并将每个容器传递到复制活动。
- **Copy**将源存储存储中的每个容器复制到目标存储中。

该模板定义两个参数：
- *SourceFilePath*是你的数据源存储的路径，你可以在其中获取容器列表。 在大多数情况下，该路径是包含多个容器文件夹的根目录。 此参数的默认值为 `/`。
- *DestinationFilePath*是要将文件复制到目标存储中的路径。 此参数的默认值为 `/`。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 请参阅在**文件存储之间复制多个文件容器**模板。 创建到源存储存储的**新**连接。 源存储存储是要从中复制多个容器中的文件的位置。

    ![与源建立新的连接](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 创建到目标存储存储的**新**连接。

    ![与目标建立新的连接](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. 选择“使用此模板”。

    ![使用此模板](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 你会看到管道，如以下示例中所示：

    ![显示管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. 选择 "**调试**"，输入**参数**，然后选择 "**完成**"。

    ![运行管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 查看结果。

    ![查看结果](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>后续步骤

- [通过 Azure 数据工厂使用控制表从数据库进行大容量复制](solution-template-bulk-copy-with-control-table.md)

- [通过 Azure 数据工厂复制多个容器中的文件](solution-template-copy-files-multiple-containers.md)