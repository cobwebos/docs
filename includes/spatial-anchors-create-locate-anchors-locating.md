---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006430"
---
## <a name="locate-a-cloud-spatial-anchor"></a>查找云空间定位点

查找以前保存的云空间定位点是使用 Azure 空间定位点的主要原因之一。 可通过多种不同的方式来查找云空间定位点。 每次可以使用一种策略来观察效果。
- 按标识符查找定位点。
- 查找已连接到以前所找到的定位点的定位点。 可在[此处](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)了解定位点关系。
- 使用[粗略重新定位](/azure/spatial-anchors/concepts/coarse-reloc/)查找定位点。

> [!NOTE]
> 每次当你查找定位点时，Azure 空间定位点都会尝试使用收集的环境数据来扩充定位点上的视觉对象信息。 如果在查找定位点时遇到问题，则可创建一个定位点，然后在不同角度和光照条件下多次定位，这样做可能有用。

如果按标识符查找云空间定位点，需要将云空间定位点标识符存储在应用程序的后端服务中，并使可供能够对其进行适当身份验证的所有设备访问。 有关示例，请参阅[教程：跨设备共享空间定位点](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)。

实例化 `AnchorLocateCriteria` 对象，设置要查找的标识符，并提供 `AnchorLocateCriteria` 在会话中调用 `CreateWatcher` 方法。