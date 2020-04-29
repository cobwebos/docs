---
title: 限制
description: SDK 功能的代码限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417685"
---
# <a name="limitations"></a>限制

许多功能具有大小、计数或其他限制。

## <a name="azure-frontend"></a>Azure 前端

* 每个进程的 AzureFrontend 实例总数：16。
* 每个 AzureFrontend 的 AzureSession 实例总数：16。

## <a name="objects"></a>对象

* 单个类型的全部允许对象（实体、CutPlaneComponent 等）：16777215。
* 允许的活动切削平面总数：8。

## <a name="materials"></a>材料

* 资产中允许的总材料：65535。

## <a name="overall-number-of-polygons"></a>多边形的总数

所有已加载模型的允许多边形数取决于 VM 在传递到[会话管理 REST API](../how-tos/session-rest-api.md#create-a-session)的大小：

| VM 大小 | 最大多边形数量 |
|:--------|:------------------|
|standard| 20000000 |
|价格| 无限制 |


## <a name="platform-limitations"></a>平台限制

**Windows 10 桌面版**

* 不支持从 Unity 部署 "PC 独立"。 请改用 UWP。
* UWP/x86 是唯一受支持的 UWP 平台。 不支持 UWP/x64。

**Hololens 2**

* 不支持[PV 相机](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)功能的呈现功能。
