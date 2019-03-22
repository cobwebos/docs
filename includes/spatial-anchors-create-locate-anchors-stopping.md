---
ms.openlocfilehash: 8e02067b32d9404a5bbdf7362b2cc32712b96250
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907620"
---
## <a name="pause-reset-or-stop-the-session"></a>暂停、 重置，或停止会话

若要停止该会话，可以调用 stop （）。 执行此操作将停止任何观察程序和环境处理，即使调用 ProcessFrame()。 然后可以调用 start （） 恢复处理。 当恢复时，维护该会话已捕获的环境数据。
