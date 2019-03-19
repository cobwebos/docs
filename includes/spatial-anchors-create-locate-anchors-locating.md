---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907628"
---
## <a name="locate-a-cloud-spatial-anchor"></a>查找云空间定位点

若要查找云空间定位标记，你将需要知道其标识符。 定位标记 Id 可以是存储在应用程序的后端服务，并可以正确地对它进行身份验证的所有设备中可以访问。 为此，请参阅示例[教程：跨设备共享空间的定位点](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)。

实例化 AnchorLocateCriteria 对象，设置你的内容，并通过提供你 AnchorLocateCriteria 在会话上调用 CreateWatcher 方法的标识符。
