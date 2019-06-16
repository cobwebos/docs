---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110737"
---
## <a name="provide-frames-to-the-session"></a>提供对会话的帧

空间定位点会话的工作原理是映射用户周围的空间。 这样做有助于确定的定位点的位置。 移动平台 （iOS 和 Android） 需要对相机源从平台的 AR 库获取帧的本机调用。 与此相反，HoloLens 正在不断扫描环境，因此无需使用 Mobile 所示的特定调用。