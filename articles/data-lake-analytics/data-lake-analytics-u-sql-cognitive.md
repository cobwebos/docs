---
title: "在 Azure Data Lake Analytics 中使用 U-SQL 认知功能 | Microsoft 文档"
description: "了解如何使用 U-SQL 的认知功能智能"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: a651fe045d7eb1265f698ebb89843fd4c2b1c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>教程：U-SQL 的认知功能入门

## <a name="overview"></a>概述
U-SQL 的认知功能使开发人员可以在其大数据程序中使用智能。 

提供以下认知功能：
* 图像处理：检测人脸
* 图像处理：检测表情
* 图像处理：检测对象（标记）
* 图像处理：OCR（光学字符识别）
* 文本：关键短语提取
* 文本：情绪分析

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>如何在 U-SQL 脚本中使用认知功能

整个过程很简单：

* 使用 REFERENCE ASSEMBLY 语句为 U-SQL 脚本启用认知功能
* 借助认知 UDO 在输入行集上使用 PROCESS 以生成输出行集

### <a name="detecting-objects-in-images"></a>在图像中检测对象

下面示例演示如何使用认知功能在图像中检测对象。

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputers.Csv();
```
有关更多示例，请查看“后续步骤”部分中的“U-SQL/认知示例”。

## <a name="next-steps"></a>后续步骤
* [U-SQL/认知示例](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [通过 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数](data-lake-analytics-use-window-functions.md)
