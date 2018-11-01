---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226531"
---
下表列出了可能的 Windows 通用和专用OS 映像的上传和捕获组合。 使用 Y 表示处理不会有任何错误的组合，使用 N 表示会出现错误的组合。下表提供了有关各种错误的原因和解决方法。

| 操作系统 | 上传专用 OS 映像 | 上传通用 OS 映像 | 捕获专用 OS 映像 | 捕获通用 OS 映像 |
| --- | --- | --- | --- | --- |
| Windows 通用 |N<sup>1</sup> |Y |N<sup>3</sup> |Y |
| Windows 专用 |Y |N<sup>2</sup> |Y |N<sup>4</sup> |

