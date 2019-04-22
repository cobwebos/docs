---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684391"
---
数据-中-网络：

- 将设备与 Azure 之间的数据，使用标准的 TLS 1.2。 为 TLS 1.1 及更早版本没有回退。 如果不支持 TLS 1.2，则将阻止代理通信。 TLS 1.2，还需要为门户和 SDK 管理操作。
- 当客户端通过本地 web UI 在浏览器中访问你的设备时，标准 TLS 1.2 用作默认安全协议。

    - 最佳做法是在浏览器配置为使用 TLS 1.2。
    - 如果浏览器不支持 TLS 1.2，可以使用 TLS 1.1 或 TLS 1.0。
- 若要保护的数据从数据服务器复制时，我们建议你使用 SMB 3.0 加密。
