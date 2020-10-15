---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006423"
---
## <a name="provide-frames-to-the-session"></a>为会话提供帧

空间定位点会话的工作方式是映射用户周围的空间。 这样做有助于确定定位点的位置。 移动平台（iOS 和 Android）需要对相机馈送进行本机调用才能从平台的 AR 库中获取帧。 相比之下，HoloLens 一直在扫描环境，因此不需要像在移动平台上那样进行特定的调用。