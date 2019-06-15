---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110620"
---
## <a name="locate-a-cloud-spatial-anchor"></a>查找云空间定位点

无法找到以前上传的云空间定位点是使用 Azure 空间的定位点库的主要原因之一。 若要查找云空间定位标记，你将需要知道其标识符。 在应用程序的后端服务中，并可以正确地对它进行身份验证的所有设备中可以访问，可以存储定位标记 Id。 为此，请参阅示例[教程：跨设备共享空间的定位点](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)。

实例化`AnchorLocateCriteria`对象，设置的标识符的内容，并调用`CreateWatcher`方法，从而在会话上的你`AnchorLocateCriteria`。
