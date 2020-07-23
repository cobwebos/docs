---
title: 迁移概述-LUIS
description: 了解迁移路径中的内容
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: a27945b5ac3dc2625c9520a8dd413b774b5d7adf
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837451"
---
# <a name="migration-in-luis"></a>LUIS 中的迁移

迁移路径上有多个项目。 使用下表了解哪些功能受到影响，以及何时需要完成迁移。

|区域|说明|迁移完成日期|
|--|--|--|
|[预测 Api](luis-migration-api-v3.md)|迁移到 V3 API。|TBD|
|[创作 API](luis-migration-authoring-entities.md)|迁移到 V3 API。|TBD|
|[创作资源](luis-migration-authoring.md)|在 LUIS 门户中使用 LUIS 创作资源从无创作资源迁移到。|2020年8月30日 |
|[必需的功能](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|此更改是在 build 大会上的5月2020，仅适用于在应用程序使用受约束功能的 v3 创作 Api。|2020年6月19日|
|[复合实体](migrate-from-composite-entity.md)|将复合实体升级到机器学习实体，以生成一个实体，该实体通过更好的 decomposability 调试实体来接收更完整的预测。|TBD|
