---
title: 限制
description: SDK 功能的代码限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6a1a51ee09422607ae1392704add4d49d3367d57
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759041"
---
# <a name="limitations"></a>限制

许多功能具有大小、数量或其他限制。

## <a name="azure-frontend"></a>Azure 前端

* 每个进程的 AzureFrontend 实例总数：16.
* 每个 AzureFrontend 的 AzureSession 实例总数：16.

## <a name="objects"></a>对象

* 单个类型允许的对象总数（Entity、CutPlaneComponent 等）：16,777,215。
* 允许的活动剪切平面总数：8.

## <a name="materials"></a>材料

* 资产中允许的材料总数：65,535。

## <a name="overall-number-of-polygons"></a>多边形总数

所有已加载模型允许的多边形数量取决于传递给[会话管理 REST API](../how-tos/session-rest-api.md#create-a-session) 的 VM 大小：

| VM 大小 | 最大多边形数量 |
|:--------|:------------------|
|standard| 2000 万 |
|高级| 无限制 |


## <a name="platform-limitations"></a>平台限制

**Windows 10 桌面**

* UWP/x86 是唯一受支持的 UWP 平台。 不支持 UWP/x64。

**Hololens 2**

* 不支持[从 PV 相机进行呈现](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)功能。
