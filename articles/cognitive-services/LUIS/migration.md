---
title: 迁移概述 - LUIS
description: 了解迁移路径中的内容
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: d6ecacf9aa1a7e650de74a412ed4f161ed0e0790
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253796"
---
# <a name="migration-in-luis"></a>在 LUIS 中迁移

迁移路径中有多个项目。 通过下表了解哪些内容受到影响以及何时需要完全迁移。

|区域|说明|迁移完成日期|
|--|--|--|
|[预测 API](luis-migration-api-v3.md)|迁移到 V3 API。|TBD|
|[创作 API](luis-migration-authoring-entities.md)|迁移到 V3 API。|TBD|
|[创作资源](luis-migration-authoring.md)|在 LUIS 门户中使用 LUIS 创作资源从无创作资源迁移到。|2020年11月2日 |
|[必需功能](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|此更改是在 2020 年 5 月的 //Build 大会上发布和执行的，仅适用于其中的应用会使用约束功能的 v3 创作 API。|2020 年 6 月 19 日|
|[复合实体](migrate-from-composite-entity.md)|通过将复合实体升级为机器学习实体来生成一个实体，该实体接收具有更好可分解性的更完整预测，以用于调试实体。|TBD|
