---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754236"
---
对于静态数据：

- 使用 BitLocker XTS AES 256 位加密来保护本地数据。
- 只有权访问共享中存储的数据。

    - 访问共享数据的 SMB 客户端需要与共享相关联的用户凭据。 创建共享时定义这些凭据。
    - 需要时创建了共享添加访问共享的 NFS 客户端的 IP 地址。