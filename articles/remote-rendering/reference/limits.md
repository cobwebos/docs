---
title: 限制
description: SDK 功能的代码限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417685"
---
# <a name="limitations"></a>限制

许多功能具有大小、计数或其他限制。

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


## <a name="platform-limitations"></a>平台限制

**视窗 10 桌面**

* 不支持从 Unity 部署"PC 独立"部署。 改用 UWP。
* UWP/x86 是唯一受支持的 UWP 平台。 不支持 UWP/x64。

**霍洛伦斯 2**

* 不支持[来自 PV 摄像机功能的渲染](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)。
