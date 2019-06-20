---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173587"
---
对于正在进行的数据：

- 标准 TLS 1.2 用于设备与 Azure 之间传输数据。 为 TLS 1.1 及更早版本没有回退。 如果不支持 TLS 1.2，则将阻止代理通信。 TLS 1.2，还需要为门户和 SDK 管理。
- 当客户端通过本地 web UI 的浏览器访问你的设备时，则标准 TLS 1.2 用作默认安全协议。

    - 最佳做法是在浏览器配置为使用 TLS 1.2。
    - 如果浏览器不支持 TLS 1.2，可以使用 TLS 1.1 或 TLS 1.0。
- 我们建议你使用 SMB 3.0 加密来保护数据时将其复制从数据的服务器。
