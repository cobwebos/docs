---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67172663"
---
## <a name="update-properties"></a>更新属性

若要更新定位点上的属性，请使用 `UpdateAnchorProperties()` 方法。 如果两个或更多设备同时尝试更新同一定位点的属性，我们将使用乐观并发模型。 这意味着第一个写入将会胜出。  所有其他写入将收到“并发”错误：在重试之前需要刷新属性。
