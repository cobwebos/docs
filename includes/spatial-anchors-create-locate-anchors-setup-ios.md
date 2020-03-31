---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67172651"
---
## <a name="setting-up-the-library"></a>设置库

调用 Start() 使会话能够处理环境数据。

若要处理会话引发的事件，请将会话的 `delegate` 属性设置为一个对象，例如视图。 此对象必须实现 SSCCloudSpatialAnchorSessionDelegate 协议。
