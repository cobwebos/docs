---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907614"
---
## <a name="update-properties"></a>更新属性

若要更新定位点上的属性，您使用 UpdateAnchorProperties 方法。 如果两个或多个设备尝试在同一时间更新为相同定位点属性，我们使用开放式并发模型。 这意味着第一次写入会获胜。  所有其他写入将出现"并发"错误： 将重试之前需要刷新属性。
