---
title: 导入应用步骤
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 4c87ea9ebb2dd7aec36025e4b45f74e186ca1cd4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588764"
---
1. 在 [LUIS 门户](https://www.luis.ai)上的“我的应用”页上，选择“+ 新建对话应用”，然后选择“导入为 JSON”。 查找上一步中保存的 JSON 文件。 无需更改应用的名称。 选择“完成”

1. 在“管理”部分的“版本”选项卡上选择 `0.1` 版本，然后选择“克隆”以克隆该版本，并为其提供一个新的名称 `ml-entity`，然后选择“完成”以完成克隆流程  。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

    > [!TIP]
    > 在修改应用之前，最佳做法是克隆到新版本。 更改完版本后，请将该版本导出为 .json 或 .lu 文件，然后将文件签入源代码管理系统中。

1. 选择“生成”，然后选择“意向”，以查看意向（LUIS 应用的主要构建基块） 。

    ![从“版本”页切换到“意向”页。](../media/tutorial-machine-learned-entity/new-version-imported-app.png)