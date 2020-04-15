---
title: 导入应用步骤
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422758"
---
1. 在[预览 LUIS 门户](https://preview.luis.ai)上的“我的应用”  页上，选择“+ 新建对话应用”  ，然后选择“导入为 JSON”  。 查找上一步中保存的 JSON 文件。 无需更改应用的名称。 选择“完成” 

1. 在“管理”部分的“版本”选项卡上选择 `0.1` 版本，然后选择“克隆”以克隆该版本，并为其提供一个新的 10 个字符的名称 `ml-entity`，然后选择“完成”以完成克隆流程    。  由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

    > [!TIP]
    > 在修改应用之前，最佳做法是克隆到新版本。 更改完版本后，请将该版本导出为 .json 或 .lu 文件，然后将文件签入源代码管理系统中。

1. 选择“生成”，然后选择“意向”，以查看意向（LUIS 应用的主要构建基块）   。

    ![从“版本”页切换到“意向”页。](../media/tutorial-machine-learned-entity/new-version-imported-app.png)