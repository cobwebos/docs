---
title: 限制
description: SDK 功能的代码限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 61085a5d28fcd74bbf6a393ddc0731e36094a63f
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617492"
---
# <a name="limitations"></a>限制

许多功能具有大小、数量或其他限制。

## <a name="azure-frontend"></a>Azure 前端

* 每个进程的 AzureFrontend 实例总数：16.
* 每个 AzureFrontend 的 AzureSession 实例总数：16.

## <a name="objects"></a>对象

* 单个类型允许的对象总数（Entity、CutPlaneComponent 等）：16,777,215。
* 允许的活动剪切平面总数：8.

## <a name="geometry"></a>几何结构

* **动画：** 动画仅限于对游戏对象的各个转换进行动画处理。 不支持具有外观动画或顶点动画的框架式动画。 不保留源资产文件中的动画跟踪。 相反，必须由客户端代码驱动对象转换动画。
* **自定义着色器：** 不支持创作自定义着色器。 只有内置 [颜色材料](../overview/features/color-materials.md) 或 [.pbr 材料](../overview/features/pbr-materials.md) 才能使用。
* 资产中**的最大不同材料数**：65535。 有关自动材料计数缩减的详细信息，请参阅 " [材料消除](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) 重复" 一章。
* **单个纹理的最大尺寸**： 16384 x 16384。 转换过程会减小更大的源纹理大小。

### <a name="overall-number-of-polygons"></a>多边形总数

所有已加载模型允许的多边形数量取决于传递给[会话管理 REST API](../how-tos/session-rest-api.md#create-a-session) 的 VM 大小：

| 服务器大小 | 最大多边形数量 |
|:--------|:------------------|
|standard| 2000 万 |
|高级| 无限制 |

有关此限制的详细信息，请参阅 " [服务器大小](../reference/vm-sizes.md) " 一章。

## <a name="platform-limitations"></a>平台限制

**Windows 10 桌面**

* Win32/x64 是唯一受支持的 Win32 平台。 Win32/x86 不受支持。

**HoloLens 2**

* 不支持[从 PV 相机进行呈现](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)功能。
