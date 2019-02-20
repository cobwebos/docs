---
title: 使用 Azure 数据工厂复制多个容器中的文件 | Microsoft Docs
description: 了解如何使用解决方案模板通过 Azure 数据工厂复制多个容器中的文件。
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
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966341"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>使用 Azure 数据工厂复制多个容器中的文件

本文中所述的解决方案模板可帮助你在文件存储之间复制多个文件、容器或存储桶中的文件。 例如，也许你想要将 Data Lake 从 AWS S3 迁移到 Azure Data Lake Store。 或者，你要将一个 Azure Blob 存储帐户中的所有内容复制到另一个 Azure Blob 存储帐户。 此模板旨在实现这些用例。

若要复制单个容器或存储桶中的文件，使用**复制数据工具**通过单个复制活动创建管道的做法会更有效。 此模板适用于比这种简单用例更复杂的用例。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板枚举源存储中的容器，然后将源存储中的每个容器复制到目标存储。 

该模板包含三个活动：
-   **GetMetadata** 活动：扫描源存储并获取容器列表。
-   **ForEach** 活动：获取 **GetMetadata** 活动提供的容器列表，然后循环访问该列表并将每个容器传递到复制活动。
-   **Copy** 活动：将源存储中的每个容器复制到目标存储。

该模板定义两个参数：
-   参数 *SourceFilePath* 是数据源存储的路径，在其中可以获取容器或存储桶的列表。 在大多数情况下，该路径是包含多个容器文件夹的根目录。 此参数的默认值为 `/`。
-   参数 *DestinationFilePath* 是目标存储中文件要复制到的路径。 此参数的默认值为 `/`。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到模板中的“在文件存储之间复制多个文件容器”，然后与源存储建立**新的连接**。 源存储是要复制其中的多个容器或存储桶中的文件的源位置。

    ![与源建立新的连接](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 与目标存储建立**新的连接**。

    ![与目标建立新的连接](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. 单击“使用此模板”。

    ![使用此模板](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 面板中会显示可用的管道，如以下示例所示：

    ![显示管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. 单击“调试”，输入**参数**，然后单击“完成”。

    ![运行管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 查看结果。

    ![查看结果](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
