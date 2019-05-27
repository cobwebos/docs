---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160232"
---
## <a name="issue-custom-image-provisioning-errors"></a>问题：自定义映像;预配错误
上传或捕获用作专用 VM 映像的通用化 VM 映像时，将发生预配错误，反之亦然。 前者会导致预配超时错误，后者会导致预配失败。 若要部署自定义映像且不出错，必须确保在捕获过程中映像类型不会更改。

下表列出了通用和专用映像的可能组合、将遇到的错误类型和修复错误需要执行的操作。

