---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110467"
---
## <a name="update-properties"></a>更新属性

若要更新定位点上的属性，您可以使用`UpdateAnchorProperties()`方法。 如果两个或多个设备尝试在同一时间更新为相同定位点属性，我们使用开放式并发模型。 这意味着第一次写入会获胜。  所有其他写入将出现"并发"错误： 将重试之前需要刷新属性。
