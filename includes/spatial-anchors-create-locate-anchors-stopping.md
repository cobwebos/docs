---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006459"
---
## <a name="pause-reset-or-stop-the-session"></a>暂停、重置或停止会话

若要暂时停止会话，可以调用 `Stop()`。 这样做会停止所有观察程序和环境处理，即使调用 `ProcessFrame()` 也是如此。 然后，可以调用 `Start()` 以恢复处理。 恢复时，将保留已在会话中捕获的环境数据。
