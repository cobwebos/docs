---
title: 限制
description: SDK 功能的代码限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680331"
---
# <a name="limits"></a>限制

由于正在运行的系统的内部详细信息，许多功能具有大小或计数限制。

## <a name="azure-frontend"></a>Azure 前端

* 每个进程的总 Azure Frontend 实例数：16。
* 每个 Azure 前端的 Azure 会话实例总数：16。

## <a name="objects"></a>对象

* 单个类型（实体、CutPlane组件等）的允许对象总数：16，777，215。
* 允许的活动切割平面总数：8。

## <a name="materials"></a>材料

* 资产中允许的材料总数：65，535。

## <a name="overall-number-of-polygons"></a>多边形的总数

所有加载的模型的允许多边形数取决于传递给[会话管理 REST API](../how-tos/session-rest-api.md#create-a-session)的 VM 的大小：

| VM 大小 | 最大面数 |
|:--------|:------------------|
|standard| 2000万 |
|溢价| 无限制 |



