---
title: "如何向 Azure 时序见解环境添加引用数据集 | Microsoft Docs"
description: "本文介绍了如何向 Azure 时序见解环境添加引用数据集。 引用数据可用于联接到源数据以增强值。"
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>使用 Azure 门户为时序见解环境创建引用数据集

本文介绍了如何向 Azure 时序见解环境添加引用数据集。 引用数据可用于联接到源数据以增强值。

引用数据集是各种项的集合，这些项已使用事件源中的事件进行了增强。 时序见解入口引擎将事件源中的事件与引用数据集中的项联接到一起。 然后即可使用此增强的事件进行查询。 该联接基于在引用数据集中定义的密钥。

## <a name="add-a-reference-data-set"></a>添加引用数据集

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 查找现有时序见解环境。 在 Azure 门户左侧的菜单中，单击“所有资源”。 选择时序见解环境。

3. 在“环境拓扑”标题下，选择“引用数据集”。

    ![创建时序见解引用数据集](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. 选择“+ 添加”添加新的引用数据集。

5. 指定唯一的“引用数据集名称”。

    ![创建时序见解引用数据集 - 详细信息](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. 在空字段中指定“密钥名称”，并选择其“类型”。 此名称和类型用于从要联接到引用数据的事件源的事件中选取正确的属性。 

   例如，如果提供的密钥名称为“DeviceId”，类型为“字符串”，则时序见解入口引擎会在每个要查找和联接的传入事件中查找名称为“DeviceId”且类型为“字符串”的属性。 可以提供多个与事件联接的密钥。 进行密钥名称匹配时区分大小写。

7. 选择“创建” 。

## <a name="next-steps"></a>后续步骤
* 以编程方式[管理引用数据](time-series-insights-manage-reference-data-csharp.md)。
* 如需完整的 API 引用，请参阅[引用数据 API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api)文档。
