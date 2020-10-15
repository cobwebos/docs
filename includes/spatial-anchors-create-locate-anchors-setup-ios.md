---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006462"
---
## <a name="setting-up-the-library"></a>设置库

调用 `Start()` 使会话能够处理环境数据。

若要处理会话引发的事件，请将会话的 `delegate` 属性设置为一个对象，例如视图。 此对象必须实现 `SSCCloudSpatialAnchorSessionDelegate` 协议。
