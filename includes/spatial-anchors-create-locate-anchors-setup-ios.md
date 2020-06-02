---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006462"
---
## <a name="setting-up-the-library"></a>设置库

调用 `Start()` 使会话能够处理环境数据。

若要处理会话引发的事件，请将会话的 `delegate` 属性设置为一个对象，例如视图。 此对象必须实现 `SSCCloudSpatialAnchorSessionDelegate` 协议。
