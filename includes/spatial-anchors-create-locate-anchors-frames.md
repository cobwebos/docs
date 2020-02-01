---
ms.openlocfilehash: 505670e719e86086dbf7721b4298ec913220f928
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694553"
---
## <a name="provide-frames-to-the-session"></a>为会话提供帧

空间定位点会话的工作方式是映射用户周围的空间。 这样做有助于确定定位点的位置。 移动平台（iOS 和 Android）需要对相机馈送进行本机调用才能从平台的 AR 库中获取帧。 与此相反，HoloLens 会持续扫描环境，因此不需要在设备（例如移动设备）上进行特定的调用。