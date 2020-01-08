---
title: 从多个容器复制文件
description: 了解如何使用解决方案模板通过 Azure 数据工厂从多个容器复制文件。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 0c4c26ba163f83483b3eb48e51d91f9a919a887c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439768"
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

模板定义以下参数：
- *SourceFileFolder*是你的数据源存储的文件夹路径，你可以在其中获取容器列表。 路径是根目录，其中包含多个容器文件夹。 此参数的默认值为 `sourcefolder`。
- *SourceFileDirectory*是数据源存储的根目录下的子文件夹路径。 此参数的默认值为 `subfolder`。
- *DestinationFileFolder*是要将文件复制到目标存储中的文件夹路径。 此参数的默认值为 `destinationfolder`。
- *DestinationFileDirectory*是要将文件复制到目标存储中的子文件夹路径。 此参数的默认值为 `subfolder`。

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
