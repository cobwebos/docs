---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "67172652"
---
与访问令牌一样，如果未设置 Azure AD 令牌，则必须处理 TokenRequired 事件，或在委托协议上实现 tokenRequired 方法。

可以通过设置事件参数上的属性来同步处理事件。
