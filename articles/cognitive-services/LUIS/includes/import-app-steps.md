---
title: 导入应用步骤
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840810"
---
1. 在[预览 LUIS 门户](https://preview.luis.ai)上的“我的应用”  页上，选择“导入”  ，然后选择“导入为 JSON”  。 查找上一步中保存的 JSON 文件。 无需更改应用的名称。 选择“完成” 

1. 在“管理”部分的“版本”选项卡上选择版本，然后选择“克隆”以克隆版本，并为其提供一个新的 10 个字符的名称，然后选择“完成”以完成克隆流程     。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

    > [!TIP]
    > 在修改应用前，最佳做法是克隆到新版本。 完成针对某个版本的操作后，将该版本导出为 .json 或 .lu 文件，然后将文件签入源代码管理系统中。

1. 选择“生成”，然后选择“意向”，以查看意向（LUIS 应用的主要构建基块）   。

    ![从“版本”页切换到“意向”页。](../media/tutorial-machine-learned-entity/new-version-imported-app.png)