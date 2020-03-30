---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67173587"
---
对于飞行中的数据：

- 标准 TLS 1.2 用于在设备和 Azure 之间传输的数据。 没有回退到 TLS 1.1 和更早版本。 如果 TLS 1.2 不受支持，代理通信将被阻止。 TLS 1.2 还需要用于门户和 SDK 管理。
- 当客户端通过浏览器的本地 Web UI 访问您的设备时，标准 TLS 1.2 将用作默认安全协议。

    - 最佳做法是将浏览器配置为使用 TLS 1.2。
    - 如果浏览器不支持 TLS 1.2，则可以使用 TLS 1.1 或 TLS 1.0。
- 我们建议您将 SMB 3.0 与加密一起加密，以保护数据从数据服务器复制时。
