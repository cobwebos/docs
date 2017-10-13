---
title: "向 Azure 时序见解环境添加引用数据集 | Microsoft Docs"
description: "在本教程中，你将引用数据集添加到时序见解环境"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.openlocfilehash: 574f3c5b3a889733f47d729c795ec39009f2ef4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>使用 Ibiza 门户为时序见解环境创建引用数据集

引用数据集是各种项的集合，这些项已使用事件源中的事件进行了增强。 时序见解入口引擎将事件源中的事件与引用数据集中的项联接到一起。 然后即可使用此增强的事件进行查询。 该联接基于在引用数据集中定义的密钥。

## <a name="steps-to-add-a-reference-data-set-to-your-environment"></a>向环境添加引用数据集的步骤

1. 登录到 [Ibiza 门户](https://portal.azure.com)。
2. 在 Ibiza 门户左侧的菜单中，单击“所有资源”。
3. 选择时序见解环境。

    ![创建时序见解引用数据集](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. 选择“引用数据集”，然后单击“+ 添加”。

    ![创建时序见解引用数据集 - 详细信息](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. 指定引用数据集的名称。
6. 指定密钥名称及其类型。 此名称和类型用于从事件源的事件中选取正确的属性。 例如，如果提供的密钥名称为“DeviceId”，类型为“字符串”，则时序见解入口引擎会在传入事件中查找名称为“DeviceId”且类型为“字符串”的属性。 可以提供多个与事件联接的密钥。 进行属性名称匹配时区分大小写。

     ![创建时序见解引用数据集 - 详细信息](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. 单击“创建”。

## <a name="next-steps"></a>后续步骤

* 以编程方式[管理引用数据](time-series-insights-manage-reference-data-csharp.md)。
* 如需完整的 API 引用，请参阅[引用数据 API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api)文档。