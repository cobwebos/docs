---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632078"
---
## <a name="pause-reset-or-stop-the-session"></a>暂停、 重置，或停止会话

若要停止会话暂时，你可以调用`Stop()`。 执行此操作将停止任何观察程序和环境处理，即使调用 ProcessFrame()。 然后可以调用`Start()`恢复处理。 当恢复时，维护该会话已捕获的环境数据。
