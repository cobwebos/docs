---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "67173587"
---
对于航班中的数据：

- 标准 TLS 1.2 用于在设备和 Azure 之间传输的数据。 不会回退到 TLS 1.1 和更早版本。 如果不支持 TLS 1.2，则将阻止代理通信。 门户和 SDK 管理还需要 TLS 1.2。
- 当客户端通过浏览器的本地 web UI 访问设备时，将使用标准 TLS 1.2 作为默认安全协议。

    - 最佳做法是将浏览器配置为使用 TLS 1.2。
    - 如果浏览器不支持 TLS 1.2，你可以使用 TLS 1.1 或 TLS 1.0。
- 建议你在将数据从数据服务器复制时，将 SMB 3.0 与加密配合使用来保护数据。
